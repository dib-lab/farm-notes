---
tags: farm, jupyter
---

[toc]

# Start Jupyter Notebook on farm

Titus Brown, ctbrown@ucdavis.edu

June 19, 2023

To edit: [![hackmd-github-sync-badge](https://hackmd.io/9Uk2wAOiQs6hnnaV_39DtQ/badge)](https://hackmd.io/9Uk2wAOiQs6hnnaV_39DtQ) / [latest on github](https://github.com/dib-lab/farm-notes/blob/latest/jupyter-on-farm.md)

(Adapted from [Nick Ulle's tutorial!](https://nick-ulle.github.io/bag-of-tricks/chapters/remote-computing.html#running-jupyter-rstudio-remotely))

## Instructions

The below uses the [mambaforge](https://github.com/conda-forge/miniforge#mambaforge) installation of conda.

First, create a mamba environment with jupyter notebook installed:
```
mamba create -y -n jup notebook nb_conda_kernels
```
Activate it:
```
mamba activate jup
```
Set a password:
```
jupyter notebook password
```

Create a script `run-jupyter-on-node.sh` containing:
```
#! /bin/bash -eu

# initialize conda
conda_base=$(conda info --base)
. ${conda_base}/etc/profile.d/conda.sh

# activate relevant environment - CUSTOMIZE ME
conda activate jup

### pick a port
read LOWERPORT UPPERPORT < /proc/sys/net/ipv4/ip_local_port_range

port="$(shuf -i $LOWERPORT-$UPPERPORT -n 1)"
while :; do
  netstat -taln | egrep ":$port.*LISTEN" || break
  port="$(shuf -i $LOWERPORT-$UPPERPORT -n 1)"
done

### print out stuff
cat <<EOF

You need to use SSH tunneling to port forward from your desktop to Jupyter Lab.

Run the following command in a new terminal on your machine:

ssh -L${port}:${HOSTNAME}:${port} $USER@farm.cse.ucdavis.edu -N

Then point your browser to http://localhost:${port}

EOF

### run me!
jupyter notebook --no-browser --ip=${HOSTNAME} --port=${port}
```

and then run with srun:
```
srun -p high2 --time=3:00:00 --nodes=1 \
            --cpus-per-task 1 --mem 5GB \
            --pty bash ./run-jupyter-on-node.sh
```
