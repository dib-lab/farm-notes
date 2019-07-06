# Partitions/queues we have access to

In slurm-speak, 'partitions' are the different queues. The primary use
of these on farm seems to be switching between small and big mem.

You specify partitions with `-p`, so e.g.

```
#SBATCH -p bml                                                                 
```

at the top of your sbatch script will specify the bml partition (big
memory, low priority).

Without specifying a partition, I've been able to allocate 30 GB but not
130 GB.

We have access to:

* bml - low priority big mem (up to a TB)
* bmm - medium priority big mem
* bmh - high priority big mem

as well as some default queue/partition that I don't yet understand :)
