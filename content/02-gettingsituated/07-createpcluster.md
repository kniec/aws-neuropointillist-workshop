+++
title = "f. Build a Cluster"
weight = 80
tags = ["tutorial", "cloud9", "ParallelCluster"]
+++
#### Configure and Build a Cluster

To configure AWS ParallelCluster, you would normally use the command
[**pcluster
configure**](https://docs.aws.amazon.com/parallelcluster/latest/ug/getting-started-configuring-parallelcluster.html)
in a terminal and provide the requested information such as the AWS
Region, Scheduler and EC2 Instance Type. This would create for you a file called `~/.parallelcluster/config` with default cluster settings. **However, today we will cut right to the chase by creating a specific configuation file for running Neuropointillist**.

The commands below generate a new keypair that allows you to access your cluster. 

```bash
# generate a new key-pair if it doesn't already exist
[ -f ~/.ssh/neuropoint-key ] || aws ec2 create-key-pair --key-name neuropoint-key --query KeyMaterial --output text > ~/.ssh/neuropoint-key
chmod 600 ~/.ssh/neuropoint-key

```

{{% notice tip %}}

Note that you cannot create a keypair with the same name twice in the
same region. For this reason, the code above checks to see whether the
key exists in the directory '~/.ssh'. If for some reason you need
clean out the key (if you get errors logging on to your cluster), you can  delete it with the command `aws ec2 delete-key-pair --key-name neuropoint-key`.
{{% /notice %}}


Paste the following commands in your terminal to create a
configuration file that creates a cluster suitable for running
neuropointillist. 


```bash
IFACE=$(curl --silent http://169.254.169.254/latest/meta-data/network/interfaces/macs/)
SUBNET_ID=$(curl --silent http://169.254.169.254/latest/meta-data/network/interfaces/macs/${IFACE}/subnet-id)
VPC_ID=$(curl --silent http://169.254.169.254/latest/meta-data/network/interfaces/macs/${IFACE}/vpc-id)
AZ=$(curl http://169.254.169.254/latest/meta-data/placement/availability-zone)
REGION=${AZ::-1}

cd ~/environment
cat > my-cluster-config.conf << EOF
[aws]
aws_region_name = ${REGION}

[global]
cluster_template = default
update_check = false
sanity_check = true

[cluster default]
key_name = neuropoint-key
base_os = ubuntu1804
vpc_settings = public
ebs_settings = myebs
compute_instance_type = c4.large
master_instance_type = c4.large
cluster_type = ondemand
placement_group = DYNAMIC
placement = compute
initial_queue_size = 2
max_queue_size = 8
s3_read_write_resource = *
scheduler = slurm
custom_ami = ami-0be03cfd7261a6a22
master_root_volume_size=60
compute_root_volume_size=60
dcv_settings=default

[dcv default]
enable = master


[vpc public]
vpc_id = ${VPC_ID}
master_subnet_id = ${SUBNET_ID}

[ebs myebs]
shared_dir = /shared
ebs_snapshot_id=snap-05f29d01489d66b1c
volume_type = gp2
volume_size = 30

[aliases]
ssh = ssh {CFN_USER}@{MASTER_IP} {ARGS}
EOF
```

Now you are ready to launch a cluster, proceed to the next step.

{{% notice tip %}}
See Appendix.b for some pointers on how to modify 
this file and how to set the networking parameters if you are working 
from your own non-AWS computer. 
{{% /notice %}}

