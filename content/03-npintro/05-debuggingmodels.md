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

In Rstudio, we want to load the `debug.Rdata` file. This will give us
access to the voxel data, the design matrix, and a function to convert
from image coordinates to a vertex.

```R
load("debug.Rdata")
ls()
```