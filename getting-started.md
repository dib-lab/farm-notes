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

## Installing conda

We recommend managing most of your software installs via conda and bioconda.

Follow instructions [here](https://docs.conda.io/en/latest/miniconda.html),
do:

```
curl -LO https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
bash Miniconda3-latest-Linux-x86_64.sh
```
and then answer yes to all the questions!

Then set up bioconda ([see docs for more info](https://bioconda.github.io/user/install.html#set-up-channels)):

```
conda config --add channels defaults
conda config --add channels bioconda
conda config --add channels conda-forge
```

Now you can use `conda install -y <packagename>` to install stuff.

There's a [nice conda tutorial](https://kaust-vislab.github.io/introduction-to-conda-for-data-scientists/) for data scientists.

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

## Running software via the slurm queuing system

Briefly, to run big/long-running jobs, you'll need to:

* create a new shell script with the commands you want to run
* put slurm SBATCH commands at the top, like so:

```
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
