# Advanced usage

## using temporary directories local to the nodes

You can sometimes achieve substantial speedups for disk-intensive jobs
by using temp space local to each node, rather than reading and
writing frequently to your home directory. This is because your home
directory is shared across all of the farm nodes, and so in some
situations writing to your home directory can be a bottleneck.  This is
especially true if you're writing and reading many small files.

In this situation, the farm admins suggest using disk local to each node.
You can do this by making a USER and JOBID specific temp directory.
The suggested incantations are:

```
# make a directory specific to user and job
export MYTMP=/scratch/${USER}/slurm_${SLURM_JOBID}
mkdir -p $MYTMP

# force clean it up after job script ends
function cleanup() { rm -rf $MYTMP; }
trap cleanup EXIT
trap cleanup USR1

# change to it!
cd $MYTMP

# run your code, telling it to use that dir:
echo running in $(pwd)

echo to copy results out, you could do
echo cp $MYTMP/files_you_want_to_keep $SLURM_SUBMIT_DIR
```

A fully functioning example is available in
`/home/ctbrown/job-scripts/use-local-temp-space.sh`

## nodes, tasks, and cpus-per-task

At the top of your batch script, you can specify the number of nodes
(`-N / --nodes`), the number of tasks (`-n / --ntasks`), and the
number of CPUs per task (`-c/--cpus-per-task`) (see
[sbatch documentation](https://slurm.schedmd.com/sbatch.html) for
confusing details). Set them like so:

```
#SBATCH -N 1
#SBATCH -n 1
#SBATCH -c 1
```

The wisdom of the ages is thus:

In general, a lot of what us bioinformaticians run is NOT cluster-aware,
so unless you have other information that something is slurm- or cluster-
adapted, you probably want to use `-N 1 -n 1` and only adjust `-c`.

For example, if I am running snakemake on a single node and want it to
be able to run 32 jobs at the same time, I would set `-c 32` in the slurm
sbatch script, and tell snakemake to use `-j 32`. Similar logic applies
to any multithreaded application that is not cluster aware: `-c` corresponds
to number of threads to use.

Thus spake @luizirber:
> --ntasks makes sense for MPI (or things that are closely tied when
>  running), which shouldn't be the case for us.

Here is an additional comment from the `--cpus-per-task` documentation:

>For instance, consider an application that has 4 tasks, each
>requiring 3 processors. If our cluster is comprised of quad-processors
>nodes and we simply ask for 12 processors, the controller might give
>us only 3 nodes. However, by using the --cpus-per-task=3 options, the
>controller knows that each task requires 3 processors on the same
>node, and the controller will grant an allocation of 4 nodes, one for
>each of the 4 tasks.
