# farm-notes

[Getting Started](getting-started.md) - accounts & installing conda/bioconda, etc.

[Integrate your github username & password](integrate_github.md)

[Partitions/queues we have access to](partitions.md)

[Example scripts](example-scripts.md)

[More advanced usage](advanced-usage.md)- includes:

* using local temp directory on nodes
* nodes, tasks, and cpus-per-task

[Using Snakemake](snakemake-slurm.md)

[Magic commands and informative links](magic-commands.md)

The appropriate help mailing list is help@cse.ucdavis.edu - the list
admins@cse.ucdavis.edu is for things that you don't want followed up on.

## Other references

New farm docs: [www.hpc.ucdavis.edu/posts/about_farm/](https://www.hpc.ucdavis.edu/posts/about_farm/)

CSE wiki: [wiki.cse.ucdavis.edu/support/systems/farm](https://wiki.cse.ucdavis.edu/support/systems/farm)

Whitehead lab wiki: [wiki/Using-the-farm-cluster](https://github.com/WhiteheadLab/Lab_Wiki/wiki/Using-the-farm-cluster)

Convenient SLURM commands (Harvard): [https://www.rc.fas.harvard.edu/resources/documentation/convenient-slurm-commands/](https://www.rc.fas.harvard.edu/resources/documentation/convenient-slurm-commands/)

## Quick tips

Copy files onto farm using port 2022:

```
scp -P 2022 here_file account@farm.cse.ucdavis.edu:there_file
```

You can also [use GLOBUS](https://wiki.cse.ucdavis.edu/tier3/globus) - see [globus docs](https://www.globus.org/data-transfer)

## Getting an account

If you have a UC Davis account, you can apply to get a farm account here:

[wiki.cse.ucdavis.edu/cgi-bin/index2.pl](https://wiki.cse.ucdavis.edu/cgi-bin/index2.pl)

Select "Farm/CAES", and "Brown."
