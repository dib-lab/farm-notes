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
export MYTMP=/scratch/$USER/$SLURM_JOBID
mkdir -p $MYTMP
cd $MYTMP

# run your code, telling it to use that dir:
echo running in $(pwd)

echo to copy results out, you could do
echo cp $MYTMP/files_you_want_to_keep $SLURM_SUBMIT_DIR
```

A fully functioning example is available in
`/home/ctbrown/job-scripts/use-local-temp-space.sh`
