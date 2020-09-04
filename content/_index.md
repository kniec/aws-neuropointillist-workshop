---
title: "Neuropointillist on AWS"
date: 2019-09-18T10:50:17-04:00
draft: false
---
# Welcome to the Neuropointillist on AWS Tutorial

This tutorial describes the use of an in-development R package (called neuropointillist) which defines functions to help scientists to run voxel-wise models using R on neuroimaging data. Why would one like to do this, rather than using a dedicated fMRI analysis packages?

First, fMRI analysis packages are generally quite limited in the models that they can run. This package can help you run structural equation models on your fMRI data if you wish.

Second, it is really instructive to understand what fMRI software is doing with your data. T statistics are so much sweeter when you have generated them with your own R code.

The [neuropointillist package](https://github.com/IBIC/neuropointillist)  has functions to combine multiple sets of neuroimaging data, run arbitrary R code (a "model") on each voxel in parallel, output results, and reassemble the data. Included are three standalone programs. npoint and npointrun use the neuropointillist package, and npointmerge  reassembles results.

This tutorial describes how to run this package on Amazon Web Services (AWS). And why would you want to do that? Writing code in R is fast (for the human) but is not as optimized for computers as fMRI packages that were developed to be more efficient. However, AWS offers you the ability to create [scalable clusters](https://aws.amazon.com/hpc/) to run this kind of massively parallel code.

However, everything that we do here can also be run on local computers. This is important because as a researcher you are likely to have varying resources available to you in different universities and at different times. It is critical to use technologies that will move with you.

Onward!








