+++
title = "a. Creating a Custom AMI for ParallelCluster"
date = 2019-09-18T10:46:30-04:00 
weight = 30 
tags = ["tutorial", "configure", "initialize", "Nextflow"]
+++

By default, AWS ParallelCluster doesn't come with lots of neuroimaging
or R goodies installed. You need to install them. But since AWS
ParallelCluster is a bit meta - it is really just a program to
describe how to create a cluster - you don't want to install the
software every time you launch. The ways to customize ParallelCluster are
described [in the
documentation](https://docs.aws.amazon.com/parallelcluster/latest/ug/tutorials_02_ami_customization.html#build-a-custom-aws-parallelcluster-ami).

If you just have one isolated binary application to run, you can just
put your code and data on the shared drive and ignore this
customization. But most neuroimaging packages like to be in particular
places, so this approach might be more trouble than it saves.

Here we describe the brute force, not recommended way to customize ParallelCluster,
because it is the easiest to do without becoming a system administrator on the side. We 
use the method of [modifying an AWS ParallelCluster AMI](https://docs.aws.amazon.com/parallelcluster/latest/ug/tutorials_02_ami_customization.html#modify-an-aws-parallelcluster-ami). 


##  Identify the base AMI
The first step is to identify the AMI that corresponds to the region 
and the operating system you select. This tutorial takes place in the *us-east-1* region, so for version 2.8.1, the correct AMI for this region and for Ubuntu 18.04
(the region and the OS and the version are important) is 
*ami-06889493c801d0916*. If there is a newer version of 
ParallelCluster, you should start with the AMI for that version to get the latest 
changes. 

Follow the directions to create and log in to your instance using the console. 

Now you are at the point of customizing your instance. The following
commands were used to install software for this workshop. They run on 
Ubuntu. 

First install R and some dependencies. Commented links include some of the instructions for these steps to explain what's happening.
```bash
#https://www.digitalocean.com/community/tutorials/how-to-install-r-on-ubuntu-18-04-quickstart
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys E298A3A825C0D65DFD57CBB651716619E084DAB9
sudo add-apt-repository 'deb https://cloud.r-project.org/bin/linux/ubuntu bionic-cran40/'
sudo apt update

# These are required for R
sudo apt-get install libglu1-mesa-dev freeglut3-dev mesa-common-dev

# Emacs is my favorite editor - install your favorite
sudo apt-get install emacs25

# Install R
sudo apt-get install r-base
```

You need to install at least `fsleyes` from the FSL package. 
```bash
# Install miniconda
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
bash  Miniconda3-latest-Linux-x86_64.sh 

# Install fsleyes
# We just start a new shell rather than logging out and back in to
# source the miniconda changes.
bash
conda install -c conda-forge fsleyes
```
If you are doing more extensive work with FSL, you can also install FSL.


We also followed directions for [installing AFNI](https://afni.nimh.nih.gov/pub/dist/doc/htmldoc/background_install/install_instructs/steps_linux_ubuntu18.html#what-to-do), ignoring the R bits because we already did that.

We can install RStudio.
```bash
#install RStudio - see docs
#https://rstudio.com/products/rstudio/download-server/debian-ubuntu/
sudo apt-get install gdebi-core
wget https://download1.rstudio.org/desktop/bionic/amd64/rstudio-1.3.1073-amd64.deb
sudo gdebi -n rstudio-1.3.1073-amd64.deb
rm rstudio-1.3.1073-amd64.deb
```

Finally, we install all the packages that we need for R. We do this by
creating a file of commands to install the packages and just run it
with Rscript. 

```bash
cat > /tmp/rscriptcommands.R << EOF
dir.create(path = Sys.getenv("R_LIBS_USER"), showWarnings = FALSE, recursive = TRUE)
install.packages("http://ascopa.server4you.net/ubuntu/ubuntu/pool/universe/r/r-cran-rniftilib/r-cran-rniftilib_0.0-35.r79.orig.tar.xz", lib=Sys.getenv("R_LIBS_USER"), repos=NULL)
install.packages("argparse", lib=Sys.getenv("R_LIBS_USER"))
install.packages("RNifti",lib=Sys.getenv("R_LIBS_USER") )
install.packages("doParallel", lib=Sys.getenv("R_LIBS_USER"))
install.packages("reticulate", lib=Sys.getenv("R_LIBS_USER"))
install.packages("nlme",lib=Sys.getenv("R_LIBS_USER"))
install.packages("lme4",lib=Sys.getenv("R_LIBS_USER"))
install.packages("lmerTest",lib=Sys.getenv("R_LIBS_USER"))
install.packages("permute",lib=Sys.getenv("R_LIBS_USER"))
EOF
Rscript /tmp/rscriptcommands.R
```
We did not install FSL or AFNI on this AMI, but you could do that if
you wanted.

Before going on to create a new AMI from this instance, make sure to 
test that your installed software completed correctly. You can 
continue to make changes to the instance and fix things until they 
work. But once you save it as an AMI, that becomes the basis of all 
new instances (including ParallelCluster instances) created from it. 

Once you save the AMI, it will have a new AMI ID. This is what you 
will use in your AWS ParallelCluster config file to launch a cluster 
that is preconfigured with a specific operating system and stable 
versions of your application software. Between the configuration file, 
the ssh key, the AMI, your file storage, and your workflow, you can create and 
recreate an entire cluster for every analysis that you do, and it will 
be perfectly reproducible.
