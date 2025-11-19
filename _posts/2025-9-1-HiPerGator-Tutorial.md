---
title: HiPerGator Tutorial
author: Jason
date: 2025-09-01
category: Jekyll
layout: post
cover: /assets/pics/hipergator/hipergator.jpg
---

HiPerGator is the University of Florida’s 4th-generation supercomputer, featuring <span style="color:#0275d8;font-weight:bold;">63 DGX B200 nodes</span> (504 NVIDIA Blackwell GPUs), <span style="color:#0275d8;font-weight:bold;">19,200 CPU cores</span>, <span style="color:#0275d8;font-weight:bold;">600 NVIDIA L4 GPUs</span>, and an <span style="color:#0275d8;font-weight:bold;">11 PB all-flash storage system</span>, set for full production in <span style="color:#0275d8;font-weight:bold;">Fall 2025</span>.

This tutorial keeps your original simple, clean, and practical style — clear structure, minimal decoration, and easy-to-copy command sections, while highlighting key ideas and warnings with color.



## 1. Quick Start
This section guides you through the very basics of connecting to HiPerGator and running your first job.



### 1.1 Connecting to HiPerGator
There are two main ways to access HiPerGator:

#### 1.1.1 SSH (command line access)
If you prefer the terminal, log in via SSH:

```bash
ssh <gatorlink_username>@hpg.rc.ufl.edu
```

Replace `<gatorlink_username>` with your UF GatorLink ID.
The first login may ask you to confirm the server fingerprint. DUO MFA is required.

Once logged in, you start in your home directory:
```
/home/$username
```

<span style="color:#0275d8;font-weight:bold;">Tip:</span> keep login node usage light — editing files, submitting jobs, checking status. <span style="color:#d9534f;font-weight:bold;">Do not run heavy CPU/GPU workloads on the login node.</span>

#### 1.1.2 Open OnDemand (web interface)
For users who prefer a graphical interface, Open OnDemand offers browser-based remote access:

👉 https://ood.rc.ufl.edu

You can launch VNC-based GUI sessions, file explorers, and interactive jobs.

> <span style="color:#d9534f;font-weight:bold;">Note:</span> The GUI provided by Open OnDemand is **VNC-based**, not a full desktop environment. Some GUI applications requiring hardware acceleration may not run properly.



### 1.2 Submit Your First Job
HiPerGator uses <span style="color:#0275d8;font-weight:bold;">Slurm</span> as the job scheduler. <span style="color:#d9534f;font-weight:bold;">Heavy computations must not be run on the login nodes.</span>

You have two main ways to run jobs:
- Batch jobs
- Interactive jobs



### Batch Job Example
Create a Slurm script:

```bash
vim test_job.slurm
```

Paste:

```bash
#!/bin/bash
#SBATCH --job-name=Example
#SBATCH --mail-type=END,FAIL
#SBATCH --mail-user=your_email@ufl.edu

#SBATCH -p your_preferred_partition
#SBATCH --qos=your_qos
#SBATCH --nodes=1
#SBATCH --ntasks=1
#SBATCH --cpus-per-task=3
# SBATCH --gres=gpu:b200:4     # Uncomment if you need GPUs
#SBATCH --mem=16GB
#SBATCH --time=00:20:00
#SBATCH --output=my_first_job_%j.log

echo "Hello from HiPerGator!"
```

Key fields to check before submitting:
- `--job-name`: short, descriptive job name
- `--mail-user`: use a valid email so you get notifications
- `-p` / `--qos`: must match the partition/QOS you are allowed to use
- `--time`: set a realistic time limit; too long can delay scheduling

Warning: Requesting more CPUs/GPUs/memory/time than your QOS allows may cause the job to be held or rejected.

Submit the job:

```bash
sbatch test_job.slurm
```

Check queue:

```bash
squeue -u $USERNAME
```

View output:

```bash
cat my_first_job_*.log
```



### Interactive Job Example
Request an interactive session on a compute node:
```bash
srun --ntasks=16 --mem=64GB --gres=gpu:1 \
     --partition=hpg-turin --time=00:20:00 \
     --pty /bin/bash
```

Use this for: debugging, testing commands, running notebooks, or exploring the environment.



## ✓ At this point, you’ve successfully:
- Logged into HiPerGator
- Submitted a Slurm job
- Retrieved output
- Started an interactive session

You now have the full basic workflow from login → job submission → output checking → interactive work.



## 2.Tricks
Everything here keeps your original concise style — short notes, simple tips, fast lookup.

### Module System (ml)
```bash
module load apptainer
module avail
module list
```

Tip: always load required modules **inside your job script or interactive session**, so the environment is correctly set up on the compute node.

### Monitor your QOS / System Status
```bash
sacctmgr show associations where user=$USER format=account,user,partition,qos,grpcpus,grpmem,grpnodes
sinfo -s
```

After you get your account and groups, do:
```
sacctmgr show account Your_Account withassoc format=account,user,fairshare,grpcpurunmins,grpcpus,grpmem
```

### SSH Tunnel (VS Code Remote Tunnels)
You can use **VS Code Remote Tunnels** to connect without manually forwarding ports:


👉 https://code.visualstudio.com/docs/remote/tunnels


On the HiPerGator login node:
```bash
code tunnel
```
Then connect from your local VS Code.


> <span style="color:#d9534f;font-weight:bold;">Warning:</span> If you SSH **from the login node directly into a compute node**, running commands such as `nvidia-smi` will show **all GPUs on the node**. This happens because GPU isolation is enforced by **Slurm**, not by SSH.
>
> <span style="color:#d9534f;font-weight:bold;">Avoid manually SSH-ing into compute nodes.</span> Always use `srun` or `salloc` so Slurm enforces proper GPU isolation.


<span style="color:#0275d8;font-weight:bold;">Tip:</span> keep the tunnel session open while you work.


