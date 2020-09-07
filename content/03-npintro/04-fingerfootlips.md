+++
title = "d. A More Complex Example"
date = 2019-09-18T10:46:30-04:00
weight = 80
tags = ["tutorial", "create", "ParallelCluster"]
+++

A more exciting and larger example of fMRI analysis with two
timepoints can be found in the [Finger, Foot, Lips test-retest fMRI dataset
](https://openneuro.org/datasets/ds000114/versions/00001), which is described in this [paper](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC3641991). We are grateful to the authors for making this dataset available in this manner to facilitate development of these models.

The motor task is a block design task that consists of blocks of
finger tapping, foot twitching, and presumably lip pursing interleaved
with visual fixation. There were two occasions of measurement so that
test-retest reliability could be evaluated.

The motivation for this particular example was really to prove to
ourselves that using R, you could analyze *raw* fMRI data using models
that seemed to make sense. This allows you to do a lot of interesting
things that might be hard to do otherwise - for example, to look at
the effect of different nuisance parameters on the signal in different
parts of the brain, or to look more carefully at fMRI reliability. 

However, normally you would use a perfectly wonderful and well
optimized package such as AFNI for fMRI first-level analysis, and use
the output statistics from a contrast or from functional connectivity
analysis in a higher-level longitudinal model with neuropointillist. 

We include a few examples of different models that one might run.
Change directories to the this example:

```bash 
cd /shared/neuropointillist/example.fingerfootlips
```

These data reside on OpenNeuro, but we have downloaded part of the
data here to save time. To download these data from scratch, if you
are not taking the Flux workshop, see instructions [here in the Quick
Start
section](http://ibic.github.io/neuropointillist/fingerfootlips.example.html). In
particular, the script `00downloadfiles` script is a simple example of
how to automatically download publicly available files from OpenNeuro
without getting too deep into AWS.

•  Look at `lmerICC.R` for an example of doing an ICC. 

•  Look at `nlmemodel.R` for an example of modeling fMRI data using the nlme package. We can’t handle lots of fixed effects easily, but we can specify autocorrelation in the data. 

To run this example in our slurm cluster, create the file `readargs.R` as follows and run `npoint`.

```bash
cd /shared/neuropointillist/example.fingerfootlips
cat > readargs.R << EOF
cmdargs <- c("-m","mask.nii.gz", "--set1", "setfilenames1.txt",
             "--set2", "setfilenames2.txt",             
             "--setlabels1", "setlabels1.csv",
             "--setlabels2", "setlabels2.csv",             
             "--model", "nlmemodel.R",
             "--output", "nlmemodel/n.",
             "--debug", "debug.Rdata",
             "--slurmN", "50")
EOF
npoint
```

This generates 50 jobs in the directory `nlmemodel` and creates sample
slurm submit scripts. Again, these need some tailoring.

As we did in the previous example, modify these scripts and submit the jobs.
```bash
# cd to the directory with the jobs
cd nlmemodel

#create a better slurm submit script
cat > slurmjob.bash << 'EOF'
#!/bin/bash
#Slurm submission options

#SBATCH --mail-type=END
#SBATCH -o npoint_%A_%a.out
#SBATCH -o npoint_%A_%a.err
export OMP_NUM_THREADS=1
MODEL=/shared/neuropointillist/example.fingerfootlips/nlmemodel.R
DESIGNMAT=n.designmat.rds
num=$(printf "%04d" $SLURM_ARRAY_TASK_ID)
npointrun -m n.${num}.nii.gz --model ${MODEL} -d ${DESIGNMAT}
EOF

# submit the job
./runme.slurm
```

•  For a much better example, look at lmermodel.R for an example of modeling fMRI data. 

•  Look at how we prewhiten the data for lmer using AFNI (`03prewhiten`). We haven't run that in advance for you so feel free to give it a try.