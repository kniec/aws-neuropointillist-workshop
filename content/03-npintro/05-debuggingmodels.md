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

