# Snakemake profiles

While https://github.com/dib-lab/farm-notes/blob/latest/snakemake-slurm.md works greatly, Snakemake has deprectated the `--cluster-config` and replaced with `--profile`.

Here's a simple example of setting this up globally on a user account.

```bash
mkdir -p ~/.config/snakemake/slurm
touch ~/.config/snakemake/slurm/{config.yaml,slurm-status.py}
chmod +x ~/.config/snakemake/slurm/slurm-status.py
```

**~/.config/snakemake/slurm/config.yaml**

```yaml
cluster:
  mkdir -p logs/{rule}/ &&
  sbatch
    --cpus-per-task={threads}
    --mem={resources.mem_mb}
    --time={resources.time}
    --job-name=smk-{rule}
    --output=logs/{rule}/{jobid}.out
    --error=logs/{rule}/{jobid}.err
    --partition={resources.partition}
    --parsable
default-resources:
  - mem_mb=2000
  - time=480
  - partition=low2
  - threads=1
jobs: 100
latency-wait: 60
local-cores: 8
restart-times: 1
max-jobs-per-second: 20
keep-going: True
rerun-incomplete: True
printshellcmds: True
scheduler: greedy
use-conda: True
conda-frontend: mamba
cluster-status: ~/.config/snakemake/slurm/slurm-status.py
max-status-checks-per-second: 10
```

**~/.config/snakemake/slurm/slurm-status.py**
```python
#!/usr/bin/env python3
import subprocess
import sys
jobid = sys.argv[-1]
output = str(subprocess.check_output("sacct -j %s --format State --noheader | head -1 | awk '{print $1}'" % jobid, shell=True).strip())
running_status=["PENDING", "CONFIGURING", "COMPLETING", "RUNNING", "SUSPENDED", "PREEMPTED"]
if "COMPLETED" in output:
  print("success")
elif any(r in output for r in running_status):
  print("running")
else:
  print("failed")
```

**Run**

```
snakemake -s <snakfile> --profile slurm

```

### Create your own profile

The example above is just a usage demo that you can modify. You can create a new parameter in the `sbatch` command as `{resources.new_parameter}` and then use the `new_parameter` in the resources section of a snakemake rule.

**Example:**

In `~/.config/snakemake/slurm/config.yaml` append the following parameter to the sbatch command, then use it in the snakemake rule `resources`.

```yaml
--nodes={resources.nodes}
```

```python
rule test_rule:
  threads: 64
  resources:
    - partition = 'med2' # med2 partition
    - mem_mb = 350 * 1024 # 350GB
    - time = 2 * 24 * 60 # two days
    - nodes = 2 # Two nodes <- new parameter
```

Please note that `job name` = `rule name`, and all log files are written in `logs/job_name/job_id`. Multiple runs to the same rule will override the old log file. 

<hr>

_Thanks to https://fame.flinders.edu.au/blog/2021/08/02/snakemake-profiles-updated_
