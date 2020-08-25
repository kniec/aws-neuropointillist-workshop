+++
title = "e. Install AWS ParallelCluster"
weight = 80
tags = ["tutorial", "cloud9", "ParallelCluster"]
+++
#### Install AWS ParallelCluster 

In this section, AWS ParallelCluster is installed on the AWS Cloud9 instance. Python and PIP (Python package management tool) are already installed in the Cloud9 environment. Use the pip install command to install AWS ParallelCluster: 

```bash 
pip install aws-parallelcluster==2.8.1 -U --user 
```

{{% notice info %}}
Note that we are specifying a specific verison of AWS ParallelCluster. We do this because we built the "disk" , or AMI, for the cluster using this specific version. If you leave this version out two years from now (currently 2020), this mismatch will probably cause your cluster build to fail. 
{{% /notice %}}
