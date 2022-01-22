# Getting started

## Getting an account

Go to the [account request form](https://wiki.cse.ucdavis.edu/cgi-bin/index2.pl
) and use 'Brown' as the supervisor.

You'll need an ssh public key to create an account.

## Logging in

To log into the head node, run:
```
ssh <ucdavis id>@farm.cse.ucdavis.edu
```

if you have used a non-default private key, use `-i <path to private key>`.

## Interactively logging in

To request compute node resources to avoid running on the head node, use `srun`. This will request 20 GB RAM for 24 hours on high priority:
```
srun -p high2 -t 24:00:00 --mem=20000 --pty bash
```
(We strongly suggest _against_ doing this regularly, and instead suggest using `sbatch` to submit scripts that run your analysis; see "Running software via the slurm queuing system" below, for more on the suggested approach.)

## Installing conda

We recommend managing most of your software installs via conda and bioconda. Please see [this tutorial on conda](https://github.com/ngs-docs/2020-GGG298/tree/master/Week3-conda_for_software_installation) if interested in more details.

To install conda, read on!

Following instructions [here](https://docs.conda.io/en/latest/miniconda.html),
do:

```
echo source ~/.bashrc >> ~/.bash_profile
curl -LO https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
bash Miniconda3-latest-Linux-x86_64.sh
```
and then answer yes to all the questions!

Log out and log back in again to activate the base conda environment. If this does not work, run:

```
source ~/.bashrc
```

Then set up bioconda ([see docs for more info](https://bioconda.github.io/user/install.html#set-up-channels)):

```
conda config --add channels defaults
conda config --add channels bioconda
conda config --add channels conda-forge
```

Now you can use `conda install -y <packagename>` to install stuff.

There's a [nice conda tutorial](https://kaust-vislab.github.io/introduction-to-conda-for-data-scientists/) for data scientists available!

## Conda environments

You can use the default `base` conda environment for most things, but if
you want to create a specialized environment, you can do:

```
conda create -n somename python==3.6
```

and then

```
conda activate somename
```

Now installs will go into this separate conda environment.

Note that you can do pip installs into a specific conda environment as well,
e.g.

```
conda activate somename
pip install Cython
```

will install the python package Cython into the conda environment `somename`.

## Running Rstudio interactively

First you need to install Rstudio using conda
```
conda create -n rstudio rstudio
```

Now log in to farm through ssh with X11 forwarding; from Mac OS X or Linux,
```
ssh -X username@farm.cse.ucdavis.edu
```
(If you use [Mobaxterm](https://mobaxterm.mobatek.net/) on Windows, you can also get X11 forwarding and display
to work; please let us know if you need help doing this!)

Start an interactive job:
```
srun -t 240 --mem=10g -p high2 --pty bash
```

Activate conda env for Rstudio and launch an instance
```
conda activate rstudio
rstudio
```
An Rstudio interface now should appear on your desktop.

## Running software via the slurm queuing system

Briefly, to run big/long-running jobs, you'll need to:

* create a new shell script with the commands you want to run
* put slurm SBATCH commands at the top, like so:

```
#SBATCH -p med2
#SBATCH -J sgc
#SBATCH -t 3-0:00:00
#SBATCH -N 1
#SBATCH -n 1
#SBATCH -c 4
#SBATCH --mem=30gb
```

* run `sbatch <scriptname>`.

A script template I use regularly is something like this:

```
#! /bin/bash -login
#SBATCH -p med2
#SBATCH -J sgc
#SBATCH -t 3-0:00:00
#SBATCH -N 1
#SBATCH -n 1
#SBATCH -c 4
#SBATCH --mem=30gb

# activate conda in general
. "/home/ctbrown/miniconda3/etc/profile.d/conda.sh"

# activate a specific conda environment, if you so choose
conda activate somename

# go to a particular directory
cd /home/ctbrown/2018-paper-spacegraphcats/pipeline-base

# make things fail on errors
set -o nounset
set -o errexit
set -x

### run your commands here!

# Print out values of the current jobs SLURM environment variables
env | grep SLURM
```

You can use `squeue | grep ctbrown` to look at the status of your jobs.

The job will output a file `slurm-<NUMBER>.out` that contains all of the
output and errors from your job.

If you need to use more memory than slurm allows (e.g. `sbatch` fails
with an error `sbatch: error: Batch job submission failed: Invalid
account or account/partition combination specified` or `sbatch: error:
CPU count per node can not be satisfied` or `sbatch: error: Batch job
submission failed: Requested node configuration is not available` then
you probably need to use a different partition; see
[Partitions/queues we have available](partitions.md).

## Shared storage
For files shared among users (references, databases, etc), use `/group/ctbrowngrp/` to avoid having redundant files.

## Using shared resources

Users in `ctbrowngrp` collectively share resources. 
Currently, this group has priority access to 1 TB of ram and 96 CPUs on
one machine, and 256 GB RAM and up to 64 CPUs on another two machines.
The big mem machine is accessed using the big mem partition, `bm*`,
while the smaller-memory machines are accessed on `high2`/`med2`/`low2`.

As of February 2020, there are 31 researches who share these resources.
To manage and share these resources equitably, we have created a set of rules for resource usage. 
When submitting jobs, if you submit to a `bm*` partition, please follow these rules:

+ `bmh`/`high2`: use for 1. small-ish interactive testing 2. single-core snakemake jobs that submit other jobs. 3. only if really needed: one job that uses a reasonable amount of resources of “things that I really need to not get bumped.” Things that fall into group 3 might be very long running jobs that would otherwise always be interupted on `bmm`/`med2` or `bml`/`low2` (e.g. > 5 days), or single jobs that need to be completed in time for a grant or presentation. If your single job on `bmh`/`high2` will exceed 1/3 of the groups resources for either RAM or CPU, please notify the group prior to submitting this job. 
+ `bmm`/`med2`: don’t submit more than 1/3 of resources at once. This counts for cpu (96 total, so max 32) and ram (1TB total, so max 333 GB).
+ `bml`/`low2`: free for all! Go hog wild! Submit to your hearts content!

Note that the `bmm`/`bml` and `med2`/`low2` queues have access to the full cluster, not just our machines; so if farm is not being highly utilized you may be able to run *more* jobs _faster_ on those nodes than on `bmh`/`high2`.

## Managing system modules

- List all available modules: `module avail`
- List currently loaded modules: `module list`
- Loading a module: `module load <module_name>`
  - Example loading GCC module: `module load gcc/9.2.0`
- Unloading module: `module unload <module_name>`
