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

## Priorities and queues

Per Bill Broadley, here is how things work:

> bmh = get what you paid for within 1 minute.
> bmm = get more than you paid for, if there's any free resources (which is usually), but your job
> might be suspended if another user asks for nodes in bmh.
> bml = get more than you paid for (just like bmm) but your job might be killed and rescheduled.

## Allocation Limits

Our group allocation has limits. As of 10/29/2019, they are:

  - 1TB of memory
  - 96 CPU
