+++
title = "a. A Neuropointillist Walkthrough"
date = 2019-09-18T10:46:30-04:00 
weight = 30 
tags = ["tutorial", "configure", "initialize", "ParallelCluster"]
+++

#### Finding Neuropointillist 
We have [installed neuropointillist for
you](http://ibic.github.io/neuropointillist/installation-linux.html)
and downloaded some data for you in a shared directory on the
cluster. Using a shared volume just makes it a bit easier to organize
your data separately from the cluster itself, sort of like a portable
USB drive that you might bring from your laptop to your desktop at work.

Go to this shared directory:

```bash 
cd /shared/neuropointillist
ls
```

Here, there are three executables:

- npoint
- npointmerge
- npointrun

*npoint* is the main executable that runs the R functions that read in
 a bunch of 4d fMRI or 3d statistics files and a model file and either
 splits them up into jobs to be run on a cluster, or runs them in
 parallel.

*npointrun* is used to run individual jobs.

*npointmerge* is used to merge all the results of split up jobs into
 output files.

#### example.rawfmri (simulated fMRI data)
Change directories to `example.rawfmri`.

```bash
cd example.rawfmri
ls
```
This directory has an example of simulated fMRI data and a model to show you how to set up an analysis of raw fMRI data. 

Edit the file `readargs.R` with your favorite text editor. (Feel free to install your favorite editor it if it's not there.)

This file is a convenience file that, if it exists in a particular
directory, specifies the arguments for npoint. You could just as
easily create a bash script to call npoint with the correct arguments,
but I thought this approach helped keep all the files straight.

The arguments specify the following: 

```
-m mask_4mm.nii.gz
```
This is a NiFTI file that contains a “1” in every voxel that should be analyzed
and a “0” in every voxel you would like to ignore. Because we only
analyze voxels that are in the brain, in grey matter, or in a
particular structure, the mask greatly cuts down on computation time.
To shorten the time to run the example, replace this file with
oneslice_4mm.nii.gz.

```
-set1 setfilenames1.txt
-set2 setfilenames2.txt
```
The “setfilenames”options specify lists of files for each timepoint. They can be absolute paths or relative from the directory that you are in.  You can specify up to five lists of files using command line flags.

```
-setlabels1 setlabels.csv
-setlabels2 setlabels.csv
```
The “setlabels” options specify any important information (subject numbers, occasion of measurement, covariates such as age or gender) for the corresponding setfile. 

There is no rule that setfiles need to consist of data from only one
timepoint. It is only a convenience to help people organize data from
longitudinal studies. You can put all the information that you need to
model in one setfilenames file and one corresponding setlabels
file. And in fact, you should definitely do that if you have more than
5 timepoints, because that is all that is supported through the
command line flags. 

```
-model fmrimodel.R
```
This flag specifies the code that you provide. This code will be run on each voxel from the voxeldat and “attaches” to the variables in the designmat so that you can use those variables in ANY WAY and save ANY THINGS out to files. 

```
-output sgedata/sim
```
This flag specifies a prefix for all the output files. 

```
-debug debug.Rdata
```
It is helpful when developing your model to test it. This debug flag specifies to write out the data structures into an Rdata file that you can read into R, to more closely figure out what is going on in particular voxels.

```
-sgeN 10
```
This flag specifies how many pieces to split up the processing.

#### Make some changes to this configuration 

We just created a cluster that uses the slurm scheduler, not
SGE. Slurm is very popular among researchers and is well
supported. So, modify the output directory to be `slurmdata` rather
than `sgedata` and change the `--sgeN` flag to be `slurmN`. Or if you
prefer, you can simply cut and paste below.

```bash
cat > readargs.R << EOF
cmdargs <- c("-m","oneslice_4mm.nii.gz", "--set1", "setfilenames1.txt",
             "--set2", "setfilenames2.txt",             
             "--setlabels1", "setlabels1.csv",
             "--setlabels2", "setlabels2.csv",             
             "--model", "fmrimodel.R",
             "--output", "slurmdata/sim.",
             "--debug", "debug.Rdata",
             "--slurmN", "10")
EOF
```

#### What npoint does
- It reads the mask and every one of the files listed in the setfiles.
- It identifies the voxels in the mask.
-  If the files are 3D statistical output from a first level analysis, or any other 3D file (dirty secret – you can put ANYTHING IN HERE), it collapses the 3D file into a 1D vector where the columns are different voxels in the mask. 
-  If the files are 4D fMRI files, it collapses each 4D file into a 2D matrix where the columns are different voxels in the mask and the rows are TRs.
-  It reads the comma-separated-value setfiles. The names of the columns are the 
- It makes sure that the number of rows in each setfiles corresponds to the number of rows generated by steps 3 or 4. In other words, if you are using 4D data, you need to provide a row for each TR. Each row might contain the convolved values of each explanatory variable. 
- It creates the data structures “voxeldat” with the collapsed MR data, and the “designmat” with the data that is used to model the MR data. 


#### The model (fmrimodel.R)

Open `fmrimodel.R` with an editor.

I won’t talk a lot more about the model here – the important things to note are:

- You define a `processVoxel` function in R that takes a voxel number (v) from the collapsed voxeldat structure. 
- Because you don’t want the code to die if there is an error at one voxel (when there are so many!) you need to trap errors carefully and make sure to return some value that you can interpret as an error (and go back to the debug file as necessary)
- You can return any number of values. The names that you give them are used to construct the output filenames.

#### Run npoint

Execute the npoint command:

```bash
npoint
```

This will read the arguments described above and generate a directory
called slurmdata with the output files. It will not actually run the
model.