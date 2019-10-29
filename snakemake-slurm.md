Snakemake via SLURM
====

Snakemake can submit each of your jobs to the slurm scheduler for you! To enable this, you need to provide the `--cluster` option to snakemake on the command line, and include all of the `sbatch` information you normally put at the top of your submission files.


```
snakemake --cluster "sbatch -A CLUSTER_ACCOUNT -t CLUSTER_TIME -p CLUSTER_PARTITION -N CLUSTER_NODES -J JOBNAME" —jobs NUM_JOBS_TO_SUBMIT
```
Notes: 
  - Most clusters would prefer if you run use an interactive session to run this, so that you're not running anything on the login nodes. However, since this process is only submitting jobs, you can alternatively run this command on tmux/screen on a login node.
 - the `--jobs` parameter allows snakemake to submit up to NUM_JOBS_TO_SUBMIT number of jobs. Just be aware of submission limits on your cluster! Snakemake will only submit jobs that can be run (input files already exist). There is a parameter called `--immediate-submit` that will submit all jobs at once, but this may be an issue if the input files for those jobs are not available when those jobs make it through the scheduling queue.

## Using a cluster configuration file

To save time you can also make a yml file containing your sbatch information, and tell snakemake where to find it.

Here's an example cluster configuration file:
```
# cluster_config.yml - cluster configuration
__default__:
    account: ACCOUNT
    partition: PARTITION
    time: 01:00:00 # time limit for each job
    nodes: 1
    ntasks-per-node: 14 #Request n cores be allocated per node.
    chdir: /directory/to/change/to
    output: a_name_for_my_job-%j.out
    error: a_name_for_my_job-%j.err
```

Even if you tell snakemake where to find this file, it's not going to use all of these parameters to submit each job - it will only use the onse you specify in the `sbatch` portion of your `--cluster` statement.

To submit with identical parameters as we used above, run snakemake like so:

```
snakemake --cluster "sbatch -A {cluster.account} -t {cluster.time} -p {cluster.partition} -N {cluster.nodes}" --cluster-config cluster_config.yml —jobs NUM_JOBS_TO_SUBMIT
```
The information within the `{}` are the parameters that snakemake will read from the `cluster_config.yml` file.

In this configuration file above, I only have information for a `__default__`, which will be used as the default for each rule. If you want to set specific time limits for each rule (or some rules), you can add that info to the file. 

For example, if I have a rule called `trimmomatic_raw`, I could add the following to my `cluster_config.yml` file to specify some different cluster parameters for that rule.

```
trimmomatic_raw:
   time: 00:45:00 # time limit for this rule only
```

For non-slurm clusters, you can change the `cluster` command to reflect the scheduling service your cluster uses. See snakemake's documentation for examples.


## Examples

### Example to run within tmux
```
source ~/.bashrc
conda activate snakemake

cd /home/ntpierce/2019-burgers-shrooms/orthofinder_work

snakemake -s diamond_blast.snakefile --use-conda --cluster "sbatch -t 0:30:00 -p bmh -N 1 -c 14 -J dmnd --mem=30gb " --jobs 200
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

snakemake -s diamond_blast.snakefile --use-conda --cluster "sbatch -t 0:30:00 -p bmh -N 1 -c 14 -J dmnd --mem=30gb " --jobs 200
```

## Additional Resources

Here's a carpentries [tutorial](https://hpc-carpentry.github.io/hpc-python/17-cluster/) you might find helpful. Note that this tutorial has a `json`-formatted cluster configuration file. `json` and `yaml` files are read identically by snakemake, but I find `yaml` to be more human-friendly! You can use either.

Take a look at the snakemake documention for cluster execution here: https://snakemake.readthedocs.io/en/stable/executable.html#cluster-execution


