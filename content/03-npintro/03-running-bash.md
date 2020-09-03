+++
title = "c. Running Locally (make)"
date = 2019-09-18T10:46:30-04:00
weight = 70
tags = ["tutorial", "create", "ParallelCluster"]
+++

You can run the job on your local machine, which makes sense if it is
short, or if you have a multicore processor. This is done with the utility `make`.

The script `runme.local` is just the line:

```bash
make -j 4
```

This starts four jobs at a time. Depending on the number of cores in your workstation, this might be too many or not enough. You can see how many cores you have on linux as follows: 

```bash
nproc
```

If you have been following this tutorial you see that you have two cores available. You can run this analysis on the master node by typing.

```bash
make -j 2
```

Make is a program that checks to see if your results exist and uses
rules to create them if they do not. All of the commands invoked by
`make` are in the file `Makefile`. Some other useful things that you
can do in this directory are:

```bash
make mostlyclean
```
This removes any of the intermediate results files but leaves the final results files, so it is easier to see what is important. You can run this after you have checked your final results and made sure they are ok.

```bash
make clean
```
This removes all of the intermediate results files and all the raw data pieces - but leaves the final results. This is a nice cleanup to do when you have finished your analysis.

```bash
make
```
If something has gone wrong somewhere and you are missing a part of your data, or if you need to merge results, this will take care of it. 