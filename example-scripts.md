# Example sbatch scripts

## Default script

Run for up to 3 days (-t), using one node (-N) with one task per node
(-n) and up to 4 CPUs per task (-c 4), and 30 GB of RAM.

Name the job `sgc` e.g. in the squeue report.

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

## Example bigmem script

Here we use the low priority bigmem partition (up to 1 TB RAM) with `-p bml`.

Run for up to 3 days (-t), using one node (-N) with one task per node
(-n) and up to 4 CPUs per task (-c 4), and 130 GB of RAM.

Name the job `sgc` e.g. in the squeue report.

```
#! /bin/bash -login
#SBATCH -p bml
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

## E-mail notifications

You can get e-mail notifications of start, finish, etc. like so:

```
#! /bin/bash -login
#SBATCH --mail-type=ALL
#SBATCH --mail-user=titus@idyll.org
#SBATCH -p bml
#SBATCH -J sgc
#SBATCH -t 3-0:00:00
#SBATCH -N 1
#SBATCH -n 1
#SBATCH -c 4
#SBATCH --mem=30gb
```

The mail-type arguments can be one of NONE, BEGIN, END, FAIL, REQUEUE, ALL,
TIME_LIMIT, TIME_LIMIT_90 (reached 90 percent of time limit), TIME_LIMIT_80
(reached 80 percent of time limit), TIME_LIMIT_50 (reached 50 percent of time
limit) and ARRAY_TASKS.  You can put multiple mail-types in a comma-separated
list.
