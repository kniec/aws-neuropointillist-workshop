+++
title = "b. Running on an AWS Cluster (slurm)"
date = 2019-09-18T10:46:30-04:00
weight = 50
tags = ["tutorial", "create", "ParallelCluster"]
+++

#### Run your model using slurm

Change into the working directory for your model as follows.

```bash
cd slurmdata
```

Here you can see a variety of files that begin with the prefix
`sim`. Each chunk has a corresponding NiFTI file mask (with the
.nii.gz extension) and its representation for R (with an .rds
extension). You can also see the `debug.Rdata` file.

The files that are used to actually run the model begin with
“runme”. 
There is  a file called `runme.slurm`, which will submit the
job to the queuing system to execute. This script uses the job submission script suggestively named `slurmjob.bash`. 

Open `slurmjob.bash` with an editor.

If you are running on your local cluster, there are a variety of
options that you might need to configure here, such as your partition,
and the amount of memory and time your job is expected to run. This is
necessary when you share a cluster with a lot of different users, and
part of the job of the scheduler is to figure out how to run the jobs
most efficiently. However, here we create the cluster with the right
resources to run the jobs so we can delete that information.

Edit the file to remove the `-p`, `--mem`, and `--time` flags. Alternatively you can recreate it by cutting and pasting below. Note that we have left instructions to email when the job is finished, but we have not configured email - so that won't work.

```bash
cat > slurmjob.bash << 'EOF'
#!/bin/bash
#Slurm submission options
#SBATCH --mail-type=END
#SBATCH -o npoint_%A_%a.out
#SBATCH -o npoint_%A_%a.err
export OMP_NUM_THREADS=1
MODEL=/shared/neuropointillist/example.rawfmri/fmrimodel.R
DESIGNMAT=sim.designmat.rds
num=$(printf "%04d" $SLURM_ARRAY_TASK_ID)
npointrun -m sim.${num}.nii.gz --model ${MODEL} -d ${DESIGNMAT}
EOF
```

You can submit your job now as follows:
```bash
./runme.slurm
```

#### Checking the status of your jobs
You can check to see how your job is doing:

```bash
squeue
```

Depending on how much time expired between when you started the
cluster and when you typed `squeue above`, you will probably see a
message like the following.


![Slurm](/images/neuropoint/slurmdrain.png)

This is because of autoscaling. When the cluster starts up, you
use the configuration file to specify some maximum number of nodes (here, 8) and a minimum to start
with (here, 2). These compute nodes are only alive if there are jobs
for them in the slurm queue. Because we spent some time looking at
scripts, they found no work and automatically shut down.

Submitting jobs to the slurm work queue triggers an autoscaling event
to start them. You can affirm that this is happening in the
console, but otherwise several maddening minutes go forward before you
see the nodes appear to slurm and the jobs begin to run.

When the nodes appear and jobs are running, you will see them:
![Slurm](/images/neuropoint/slurmrunning.png)

You can also get information about the nodes that have come online.
```bash
sinfo
```

Finally, you can cancel a job using the number from the `squeue` command. For example, this command cancels job number 128.
```bash
scancel 128
```


![Slurm](/images/neuropoint/sinfo.png)

Notice that AWS ParallelCluster only starts as many nodes as it
needs to to do the work. Each instance in our cluster has two virtual
CPUs, so that means that two jobs can run on each compute node. To
execute 8 jobs, we need to start 4 nodes.

When everything is complete, the nodes will shut down again. 


#### Assembling results
This is a small job and should finish pretty quickly. When it is done, you will have a lot more files in your directory, which contain the values you returned from your `processVoxel` command. We broke up the mask into 8 pieces, and now we need to reassemble them.

![Fmri example](/images/neuropoint/fmriexampledone.png)

We can do so as follows:
```bash
make
```
