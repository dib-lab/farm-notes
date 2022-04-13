# Magic commands and links

## listing members of the group

`cat /etc/group | grep ctbrowngrp`

## getting job listings for the group:

`squeue -A ctbrowngrp`  
`squeue -A ctbrowngrp | egrep -v 'bml|low2'` # jobs on med and high partition

## status web page

[link](http://stats.cse.ucdavis.edu/ganglia/?c=Agri&m=load_one&r=hour&s=descending&hc=4&mc=2)

## `node-info.sh`:
```
#!/bin/bash
partitions=`scontrol show partition | grep PartitionName | cut -d '=' -f2 | sed '/high/d;/med/d;/bigmemh/d;/bigmemm/d;/bigmemht/d;/bml/d;/bmm/d;/bmh/d' | tr '\n' ','`

sinfo -Nle -p $partitions -o "%n '%C' %O %e %m %T" | tr ' ' '\t' | sed '1d' | expand -t 10
```

## Remembering how to login

`history | grep ssh`

## Taylor's useful UNIX one liners

[link](https://gist.github.com/taylorreiter/746bdb6dee2ceac04dffb8b593bcdbac)
