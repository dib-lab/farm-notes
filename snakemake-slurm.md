# Snakemake via SLURM

Snakemake can submit each of your jobs to the slurm scheduler for you! To enable this, you need to provide the `--cluster` option to snakemake on the command line, and include all of the `sbatch` information you normally put at the top of your submission files.


```
snakemake --cluster "sbatch -A CLUSTER_ACCOUNT -t CLUSTER_TIME -p CLUSTER_PARTITION -N CLUSTER_NODES -J JOBNAME" --jobs NUM_JOBS_TO_SUBMIT
```
Notes: 
  - Most clusters would prefer that you use an interactive session (or sbatch) to run this, so that you're not running anything on the login nodes. Since this process is only submitting jobs, you _can_ run this command on tmux/screen on a login node, but only do it for a small number of jobs or you'll slow everyone up and your job will probably be killed by admin.
 - the `--jobs` parameter allows snakemake to submit up to NUM_JOBS_TO_SUBMIT number of jobs, but please be aware of submission limits on your cluster. By default, snakemake will only submit jobs that can be run (input files already exist). There is a parameter called `--immediate-submit` that will submit all jobs at once, but this may be an issue if the input files for those jobs are not available when those jobs make it through the scheduling queue.

## Using a cluster configuration file

To save time you can also make a yml file containing your sbatch information, and tell snakemake where to find it.

Here's an example cluster configuration file:
```
# cluster_config.yml - cluster configuration
__default__:
    account: ctbrowngrp
    partition: bml
    time: 1-00:05:00 # time limit per job (1 day, 5 min; fmt = dd-hh:mm:ss)
    nodes: 1 # per job
    ntasks-per-node: 1 # per job
    chdir: /home/ctbrown/charcoal  # working directory for batch script
    output: slurm-%j.out
    error: slurm-%j.err
```

snakemake will use the values in this file to fill in the parameters for job submission (see below command line, where `cluster.time` will be filled in as
`1-00:05:00` from the above config file).

Even if you tell snakemake where to find this file, it's not going to use all of these parameters to submit each job - it will only use the ones you specify in the `sbatch` portion of your `--cluster` statement.

Here, `ctbrowngrp` should correspond to the buyin account. The
partitions (queues) are described
[here](https://github.com/dib-lab/farm-notes/blob/master/partitions.md).

To run snakemake so that it submits jobs with parameters from the
config file, run snakemake like so:

```
snakemake --cluster "sbatch -A {cluster.account} -t {cluster.time} \
    -p {cluster.partition} -N {cluster.nodes}" \
    --cluster-config cluster_config.yml --jobs 8
```

The information within the `{}` are the parameters that snakemake will
read from the `cluster_config.yml` file. Here we are telling snakemake to
run up to 8 jobs simultaneously.

In this configuration file above, I only have information for a
`__default__`, which will be used as the default for each rule. If you
want to set specific time limits for each rule (or some rules), you
can add that info to the file.

For example, if I have a rule called `trimmomatic_raw`, I could add
the following to my `cluster_config.yml` file to specify some
different cluster parameters for that rule.

```
trimmomatic_raw:
   time: 00:45:00 # time limit for this rule only
```

For non-slurm clusters, you can change the `cluster` command to
reflect the scheduling service your cluster uses. See snakemake's
documentation for examples.

## Examples

### Example to run within tmux
```
source ~/.bashrc
conda activate snakemake

cd /home/ntpierce/2019-burgers-shrooms/orthofinder_work

snakemake -s diamond_blast.snakefile --use-conda --cluster "sbatch -t 0:30:00 -N 1 -c 14 -J dmnd --mem=30gb " --jobs 5
```

### Example to submit as a job
```
#!/bin/bash -login
#SBATCH -D /home/ntpierce/2019-burgers-shrooms/orthofinder_work
#SBATCH -J dmnd_snake 
#SBATCH -t 3-0:00:00
#SBATCH -N 1
#SBATCH --output /home/ntpierce/2019-burgers-shrooms/orthofinder_work/dmnd_snake-%j.out
#SBATCH --error /home/ntpierce/2019-burgers-shrooms/orthofinder_work/dmnd-snake-%j.err

# activate conda in general
source /home/ntpierce/.bashrc # if you have the conda init setting

# activate a specific conda environment, if you so choose
conda activate snakemake 

# go to a particular directory
cd /home/ntpierce/2019-burgers-shrooms/orthofinder_work 

# make things fail on errors
set -o nounset
set -o errexit
set -x

### run your commands here!

snakemake -s diamond_blast.snakefile --use-conda --cluster "sbatch -t 0:30:00 -N 1 -c 14 -J dmnd --mem=30gb " --jobs 5
```

## Additional Resources

A simple fully functioning example for the farm cluster is
[here](https://github.com/ctb/2019-snakemake-slurm).

Here's a carpentries
[tutorial](https://hpc-carpentry.github.io/hpc-python/17-cluster/) you
might find helpful. Note that this tutorial has a `json`-formatted
cluster configuration file. `json` and `yaml` files are read
identically by snakemake, but I find `yaml` to be more human-friendly!
You can use either.

Take a look at the snakemake documention for cluster execution [here](https://snakemake.readthedocs.io/en/stable/executable.html#cluster-execution).

Tessa has written a nice blogpost about [using Snakemake Profiles](https://bluegenes.github.io/Using-Snakemake_Profiles/), too.
