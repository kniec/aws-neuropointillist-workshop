+++
title = "e. Debugging Models"
date = 2019-09-18T10:46:30-04:00
weight = 90
tags = ["tutorial", "create", "ParallelCluster"]
+++

The models we have described do not spring forth from the keyboard
without a lot of trial and error! In general, your neuropointillist workflow looks something like this:

- Gather the data 
- Create a dummy model file 
- Create a `readargs.R` file
- Run neuropoint to obtain a debug file
- Interactively examine your data and test your real model
- Run neuropoint again to generate the jobs
- Submit that to a cluster, or otherwise in parallel

Here, we will use the `debug.Rdata` file created for the
Finger/Foot/Lips example to show how to go back and forth between a
voxel in three dimensional space and a voxel within neuropointillist,
to access the fMRI data for model development. 

Note that commands in this section alternate between bash commands in
a terminal, and R commands.

### Loading the Debug Data

Go into the `nlme` directory and launch `rstudio` as below.
```bash
cd /shared/neuropointillist/example.fingerfootlips/nlmemodel.canned
rstudio
```

In Rstudio, we want to load the `debug.Rdata` file. 
```R
load("debug.Rdata")
ls()
```

You will see that you now have access to the voxel data, the design
matrix, and a function to convert from image coordinates to a vertex.

Inspect the design matrix and a little bit of the voxeldata.
```R
dim(designmat)
dim(voxeldat)
head(designmat)
head(voxeldat[,1:5]
```

Let us load the `nlmemodel.R` function in the R console, and also open it up so that we can view it (using the File->Open File menu).
```R
source("nlmemodel.R")
```

Before neuropoint executes the `processVoxel` function, it "attaches"
to the design matrix so that it can access all the rows as
variables. Then the code runs on each voxel *v*. You can do the same
thing to test a model. Here, we just pick voxel number 1000 randomly.
We run both the `processVoxel` function and the model from that function.

```R 
attach(designmat)
v <- 1000
x <- processVoxel(v)
BRAIN <- voxeldat[,v]
mod <- lme(BRAIN ~ Finger+Foot+Lips+WhiteMatter+X+Y+Z+RotX+RotY+RotZ, random=~1|idnum, method=c("ML"), na.action=na.omit, corr=corAR1(form=~1|idnum), control=lmeControl(returnObject=TRUE,singular.ok=TRUE))
```

Moving away from Rstudio and to a terminal, use `fsleyes` to open one
of the output statistics files, or a structural brain image, and
choose a voxel that is interesting for the model. Here, we choose a voxel that is activated by the lip contrast: x=36, y=31, z=29. You can then translate that to a vertex in R.

Moving back to Rstudio,
```R
v <- imagecoordtovertex(36,31,29)
x <- processVoxel(v)
x
```

