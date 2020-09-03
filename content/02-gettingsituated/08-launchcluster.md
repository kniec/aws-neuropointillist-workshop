+++
title = "g. Launch a Cluster"
weight = 90
tags = ["tutorial", "cloud9", "ParallelCluster"]
+++
#### Launch a Cluster

The configuration file describes a cluster that you can then "create",
using AWS resources that will then incur a cost. To create the
cluster, type the following.

```bash
pcluster create npointclust -c my-cluster-config.conf
```

This creates a cluster called `npointclust`. The process takes a
little time, during which you will get status updates. At the end you
will get output that looks like this (your IP addresses will be different).

![Cluster output](/images/neuropoint/clusterout.png)

{{% notice tip %}}
If you are playing around with configuration files and your 
configuration fails, you will need to choose a new name for your 
cluster until the old one is fully deleted. 
{{% /notice %}}


#### Open a desktop on the cluster master
Now we will open a remote desktop to the master node of the cluster as follows:

```bash
pcluster dcv connect -k ~/.ssh/neuropoint-key npointclust
```

Get Ready!  Get Set! You will receive a long long link and you get exactly 30
seconds to open this link in a browser window. Be sure to select the
entire link.

Depending on your browser, you might get a warning. You can bypass
this by clicking *Advanced* and then *Accept the Risk and Continue*.

You can also use `ssh` to access the cluster as follows.
```bash
pcluster ssh npointclust -i ~/.ssh/neuropoint-key
```





