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

## example script for using local temp directories

If you're doing I/O intensive things (reading from a large file
repeatedly, and/or writing a lot of things to disk) it is generally
a good idea to avoid using the shared/networked disks like your home
directory. This is because those disks can become a bottleneck - if
you are running a dozen jobs on a dozen different nodes, then your
compute and memory will be separate, but you'll still be writing to one
disk!

You may also be running into locking or read/write errors that come from
using a shared or networked disk.

Luckily, the farm admins are On It and each farm node has a shared
local disk that can be (ab)used at will. This disk is named `/scratch`
and is only accessible to programs running on that node, which means
you have to explicitly copy stuff to that location from your shared
space before running your analysis program, and then again backto your
shared space after your program ends.

The script below (courtesy of Erich Schwarz, account name ems) sets up
an InterPro scan process that uses `/scratch/ems` (the local disk to each
node) to store the InterPro database

```
#!/bin/bash -login
#SBATCH --nodes=1
#SBATCH --partition=bmm
#SBATCH --time=072:00:00
#SBATCH --cpus-per-task=16
#SBATCH --job-name=job_iprscan_nigoni_2020.03.29.01.sh
#SBATCH --mem=32gb
#SBATCH --mail-type=ALL
#SBATCH --mail-user=ems394@cornell.edu

cd $HOME/wallacei/interpro/nigoni ;

# delete any previous files in the way:
rm -rf /scratch/ems/InterPro ;

# copy the interpro database from home directory over to /scratch/ems
mkdir -p /scratch/ems/InterPro ;
rsync -av /home/ems/InterPro/interproscan-5.42-78.0 /scratch/ems/InterPro 1>log.rsync.job_iprscan_nigoni_2020.03.29.01.out 2>log.rsync.job_iprscan_nigoni_2020.03.29.01.err ;

# report that copy is done to a logfile under the home directory
echo -n 'Completed rsync of interproscan-5.42-78.0 on: ' > $HOME/wallacei/interpro/nigoni/log.job_iprscan_nigoni_2020.03.29.01.txt ;
date >> $HOME/wallacei/interpro/nigoni/log.job_iprscan_nigoni_2020.03.29.01.txt ;

# delete any previous files in the way:
rm -rf /scratch/ems/wallacei/interpro/nigoni ;

# copy the query fasta file to the scratch space
mkdir -p /scratch/ems/wallacei/interpro/nigoni ;
cp -ip $HOME/wallacei/proteomes/nigoni.pc_gen.vs.nigoni_cDNA_2016.11.02.complete.max_isos.fa /scratch/ems/wallacei/interpro/nigoni ;
echo -n 'Completed copying of nigoni proteome on: ' >> $HOME/wallacei/interpro/nigoni/log.job_iprscan_nigoni_2020.03.29.01.txt ;
date >> $HOME/wallacei/interpro/nigoni/log.job_iprscan_nigoni_2020.03.29.01.txt ;

# go to the scratch space / make it your working directory
cd /scratch/ems/wallacei/interpro/nigoni ;
. $HOME/anaconda2/etc/profile.d/conda.sh ;
conda activate openjdk_11.0.1 ;

# run interpro
/scratch/ems/InterPro/interproscan-5.42-78.0/interproscan.sh -cpu 15 -dp -iprlookup -goterms -i nigoni.pc_gen.vs.nigoni_cDNA_2016.11.02.complete.max_isos.fa -b nigoni.Yin2018.max_isos -T temp.01 ;
conda deactivate ;
echo -n 'Completed InterProScan of nigoni proteome on: ' >> $HOME/wallacei/interpro/nigoni/log.job_iprscan_nigoni_2020.03.29.01.txt ;
date >> $HOME/wallacei/interpro/nigoni/log.job_iprscan_nigoni_2020.03.29.01.txt ;

# Rescue all results from scratch back to user space:
cd /scratch/ems/wallacei/interpro/nigoni ;
rm -rf temp.01 ;
mkdir -p $HOME/wallacei/interpro/nigoni/job_iprscan_nigoni_2020.03.29.01.results ;
# Copy back *contents* of the working results directory in unstable /scratch to a specifically-named results directory in stable $HOME.
cp -a /scratch/ems/wallacei/interpro/nigoni/* $HOME/wallacei/interpro/nigoni/job_iprscan_nigoni_2020.03.29.01.results ;
echo -n 'Rescued InterProScan results of nigoni proteome on: ' >> $HOME/wallacei/interpro/nigoni/log.job_iprscan_nigoni_2020.03.29.01.txt ;
date >> $HOME/wallacei/interpro/nigoni/log.job_iprscan_nigoni_2020.03.29.01.txt ;
```
