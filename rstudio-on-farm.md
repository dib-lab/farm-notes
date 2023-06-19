---
tags: farm, rstudio
---

[toc]

# Running rstudio server on farm - June 19, 2023

Titus Brown, ctbrown@ucdavis.edu

June 19, 2023


To edit: [![hackmd-github-sync-badge](https://hackmd.io/ocS5H5CnTAm_EWugvYF_Mw/badge)](https://hackmd.io/ocS5H5CnTAm_EWugvYF_Mw) / [latest on github](https://github.com/dib-lab/farm-notes/blob/latest/rstudio-on-farm.md)

## Log into farm

Via ssh, as usual!

## Allocate computational resources with srun

Allocate computational resources interactively via srun, and use a high priority queue.

For example, this:
```
srun -p high2 --time=0:30:00 --nodes=1 \
    --cpus-per-task 2 --mem 5GB --pty /bin/bash
```
will allocate 5 GB of RAM for 30 minutes on the `high2` partition/queue.

::::warning
Note: the memory, CPUs, and time you allocate here will apply to your RStudio Server session!
::::

## Recommended: activate a conda environment

To use your own conda installation of R; if so, you'll need to have a pre-existing conda environment, or else you'll have to create one (see Appendix, below, for instructions). If you create one, you only need to do this once.

Activate the conda environment like so:
```
conda activate r_env
```
where `r_env` is the name of your conda environent with `r-base` installed.

::::info
### Why use conda?
There are several good reasons to use conda to install your own version of R. These reasons include:

* you can install many R packages directly from conda-forge without compiling them; e.g. `conda install r-tidyverse` to install tidyverse.
* you can install your own packages from source as well, using e.g. devtools (`conda install r-devtools`)
* you can pick from a wide variety of R versions! List them all with `conda list r-base`

See the appendices to this document for instructions on installing conda and creating an R environment.
::::

## An easy alternate: activate the R module

If, instead of using conda, you want to use a system-installed R package, run:
```
module load R
```
You can select specific versions as listed by `module avail R`.

::::warning
If you have conda installed, you may need to run the following to use the system provided R module:
```
conda deactivate
unset CONDA_EXE
```
::::

## Load and run rstudio server

```
module load rstudio-server
rstudio-launch
```

This will print out some specific instructions; you will need the ssh information as well as the RStudio Server password.

Note that this information will change every time you run `rstudio-launch`: it is specific to your account and session!

::::warning
Note: `rstudio-launch` is what is running RStudio Server! If it exits (because srun runs out of time, or you hit CTRL-C), your RStudio Server will stop as well!
::::

## Set up ssh tunnel

Create a new ssh connection into farm; on Mac OS and Linux computers, you can create a new terminal on your desktop/laptop computer and copy/paste from the instructions printed out by `rstudio-launch` above.

For example, you will want to run something like:
```
ssh -L35181:cpu-3-51:35181 xyz@farm.hpc.ucdavis.edu
```
in a new window on your Mac OS X/Linux laptop/desktop

On Windows it's slightly trickier and depends on what software you are running.

## Open the RStudio Server Web page and log in

Now, open the URL printed out by `rstudio-launch` in a Web browser, and enter the provided username and password.

You should now be connected to RStudio Server! :tada: 

## Log out

At the end of your session, you can just close the tab and your ssh window(s).

If you want to be polite, you can more explicitly release the computational resources and shut things down by doing the following:

* use the File... menu in RStudio Server to close the session.
* type CTRL-C in the window where you ran `srun` and `rstudio-launch`.
* log out of your `srun` session by typing `exit`.
* log out of farm in both windows.

## Appendix: create a conda environment containing R

(You'll need to have conda installed before this; see below. If you have `(base)` in your prompt, you're good to go!)

To create a conda environment named `r_env` with R v4.3.0 installed, run:
```
conda create -n r_env -y r-base=4.3.0
```

### Appendix: installing conda

There are several ways to install conda; we recommend using [mambaforge](https://github.com/conda-forge/miniforge#mambaforge).

Here are some commands you can copy/paste:
```
echo source ~/.bashrc >> ~/.bash_profile
curl -LO https://github.com/conda-forge/miniforge/releases/latest/download/Mambaforge-Linux-x86_64.sh
bash Mambaforge-Linux-x86_64.sh 
```
The last command will run a program that will ask a number of questions before installing conda; answer "yes" to all of them.

Then, log out and log back in.

Your shell prompt should now have `(base) ` at the beginning, indicating that conda was installed and you are in the base conda environment.

For documentation on using conda, see [this tutorial](https://ngs-docs.github.io/2021-august-remote-computing/installing-software-on-remote-computers-with-conda.html).
