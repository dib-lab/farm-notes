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

* bml - low priority big mem node (up to a TB)
* bmm - medium priority big mem node
* bmh - high priority big mem node

as well as

* low2 - low priority compute nodes
* med2 - medium priority compute nodes
* high2 - high priority compute nodes

## Priorities and queues

Per Bill Broadley, here is how things work:

> bmh = get what you paid for within 1 minute.
> bmm = get more than you paid for, if there's any free resources (which is usually), but your job
> might be suspended if another user asks for nodes in bmh.
> bml = get more than you paid for (just like bmm) but your job might be killed and rescheduled.

## Allocation Limits

Our group allocation has limits.

As of June 15, 2020, they are as follows.

On bmh/bmm/bml, we have one bigmem node, so:

- 1TB of memory max
- 96 CPU

On high2/med2/low2, we have two parallel nodes, so:

- 512 GB RAM total, 256 GB max per job
- 32 cores on one machine, 64 cores on the other machine
