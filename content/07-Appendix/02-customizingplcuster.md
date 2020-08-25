+++
title = "b. Generating your own cluster configuration"
date = 2019-09-18T10:46:30-04:00 
weight = 40 
tags = ["tutorial", "configure", "initialize", "Nextflow"]
+++

We used a neat trick with AWS EC2 instance "metadata" to identify
network parameters to configure AWS ParallelCluster. But this won't
work for you if you would like to run ParallelCluster from your
desktop. And I assume you do not want a lesson on cloud networking. 

You can configure this networking using the AWS ParallelCluster
configure wizard.

After configuring your AWS credentials, type

```bash
pcluster configure
```

Answer the questions as follows:
- Select your region (the region closest to where you, or your data,
are located. You do not need to use us-east-1 as we did in this workshop).
- Select a key that you can put your fingers on (via the keyboard). It
  should be stored in your `~/.ssh` directory.
- Select slurm as your scheduler.
- Select Ubuntu 18.04 as your operating system.
- Select minimum cluster size to be 0
- Select maximum cluster size to be whatever you want - but bear in
  mind that you might run into some [AWS service quotas](https://aws.amazon.com/premiumsupport/knowledge-center/manage-service-limits/)  that are in place
  to make sure people don't run machines out of control. You can ask
  to have these increased.
  - Accept defaults for "Automating VPC creation"
  - Accept default for "automate Subnet creation"
  - Choose default network configuration.

This process will create a file for you called
`~/.parallelcluster/config` that you can edit to include the
parameters from our cluster configuration - notably a custom AMI if
you need it, and the DCV configuration parameters.  I would also
suggest using the `/shared` snapshot data directory just as we did
it. You can treat a shared drive sort of like a USB drive that you
move around from machine to cluster. You can back these up by taking a
[snapshot](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSSnapshots.html).


{{% notice tip %}}
Notice that there are sections in the configuration file for "ebs" and
"dcv", etc. These sections are named and need to be referred to in the
"cluster" section of the config file. If you forget to do this, your
customizations might be silently ignored, and you will get
uninterpretable errors.
{{% /notice %}}
