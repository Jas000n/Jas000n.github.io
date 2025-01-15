---
title: Tutorial on NYU HPC(High Performance Computer)
author: Jason
date: 2023-9-16
category: Tutorial
layout: post
cover: /assets/pics/nyu_hpc/hardware_specs.png
---
In modern research, High Performance Computing (HPC) has become an indispensable tool, especially in fields requiring the processing of large datasets or the undertaking of complex simulations. NYU's HPC resources provide powerful computing capabilities to help researchers and students accelerate their research.

This tutorial is designed to guide both beginners and experienced users on how to effectively utilize NYU’s HPC facilities.

A special focus will be given to working with Singularity containers and overlay images, which are crucial for ensuring that your computing environment is both flexible and reproducible.

# 1. Create dir for all overlay images
```shell
mkdir /scratch/$USER/overlay_images
cd /scratch/$USER/overlay_images
```
# 2. Browse and copy an appropriate gzipped overlay images from the overlay directory
```shell
# browse all overlay images 
ls /scratch/work/public/overlay-fs-ext3

# copy a suitable one for your environment
cp -rp /scratch/work/public/overlay-fs-ext3/overlay-50G-10M.ext3.gz .
gunzip overlay-50G-10M.ext3.gz # unzip might take a while on login node

# rename that image to your project 
mv overlay-50G-10M.ext3 my_pytorch.ext3
```
# 3. Browse and find the appropriate Singularity container 
```shell
# browse all singularity containers
ls /scratch/work/public/singularity/
```
# 4. Start an interactive job with adequate compute and memory resources, launch singularity container in read/write mode (with the :rw flag) to install packages.
```shell
srun --cpus-per-task=2 --mem=10GB --time=04:00:00 --pty /bin/bash
# wait to be assigned a node

singularity exec --nv --overlay my_pytorch.ext3:rw /scratch/work/public/singularity/cuda11.6.124-cudnn8.4.0.27-devel-ubuntu20.04.4.sif /bin/bash

```
# 5. Install Miniconda and create a wrapper script
```shell
wget https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh
bash Miniconda3-latest-Linux-x86_64.sh -b -p /ext3/miniconda3
rm Miniconda3-latest-Linux-x86_64.sh # if you don't need this file any longer

vim /ext3/env.sh
```
paste the following codes in env.sh
```shell
#!/bin/bash

source /ext3/miniconda3/etc/profile.d/conda.sh
export PATH=/ext3/miniconda3/bin:$PATH
export PYTHONPATH=/ext3/miniconda3/bin:$PATH
```
# 6. Create conda virtual environment and Install packages 
```shell
conda create -n myenv python=3.10
conda activate myenv
pip install -r requirements.txt # install dependencies
exit # exit that singularity container after finish
exit # exit that assigned computation node
```
# 7. (After setting up the env) Run that singularity using slurm
create sbatch file:
```shell
vim my_sbatch.sbatch
```
paste the following script:
```shell
#!/bin/bash

#SBATCH --nodes=1
#SBATCH --ntasks-per-node=1
#SBATCH --cpus-per-task=32
#SBATCH --time=24:00:00
#SBATCH --mem=128GB
#SBATCH --gres=gpu:a100:4
#SBATCH --job-name=My_project
#SBATCH --output=python-hello-world.out

module purge

singularity exec --nv \
	    --overlay /scratch/$USER/overlay_images/my_pytorch.ext3:ro \
	    /scratch/work/public/singularity/cuda11.3.0-cudnn8-devel-ubuntu20.04.sif\
	    /bin/bash -c "source /ext3/env.sh; conda activate myenv; cd /scratch/$USER/myProject/;nohup python training.py &"

```
submit that job to slurm:
```shell
sbatch my_sbatch.sbatch
```
# 8. Check job status
```shell
squeue -u $USER
```
