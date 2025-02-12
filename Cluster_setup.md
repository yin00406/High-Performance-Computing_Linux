# Cluster setup

## Website

## Operation basics - MSI

```shell
module list # currently loaded module files（软件等）
module avail # view all of the loadable software
module load fileName # load 某一个文件
# 显示配额
	groupquota # msi
	gladeguota # ncar
python xxx.py # 运行 .py 文件，一般用来测试小程序，大程序直接放到 cluster 上运行
softwareName # 直接打开某一个软件，比如 MATLAB
```

## Operation basics - Anvil

```shell
 mybalance # check the balance of SUs
 myquota # check storage
```

## CPU related operations

```shell
lscpu # You can find the number of threads (CPUs) etc.
```

## GPU related operations

### GPU info

```python
nvidia-smi # show percentage of GPU usage
nvidia-smi -L # show the basic info of GPU

kill -9 {PID} # kill a process in nvidia gpu
```

### Apply for multi GPUs and use them separately

```python
# after you applied multi-GPUs, you should enter
CUDA_VISIBLE_DEVICES=0 python xxx.py
CUDA_VISIBLE_DEVICES=1 python xxx.py
...
```

## Disk usage and files counting and download

```shell
groupquota # MSI
```

## Problems encountered in use

#### 1. Unexpected error from cudaGetDeviceCount()

Open a new Tab (i.e., rebooting)

#### 2. sbatch error

```sh
sbatch: error: Batch script contains DOS line breaks (\r\n)
sbatch: error: instead of expected UNIX line breaks (\n)
```

`dos2unix clusterMap_SS`

#### 3. SyntaxError: EOL while scanning string literal 

Files, like the txt file, should be converted by `dos2unix xxx.txt`

## Globus

### [Use Globus to transfer data at MSI?](https://www.msi.umn.edu/support/faq/how-do-i-use-globus-transfer-data-msi-0)

### Transfer files between the HPC and the local machine quickly

- [Globus Connect Personal](https://www.globus.org/globus-connect-personal)

- [How To Install, Configure, and Uninstall Globus Connect Personal for Windows](https://docs.globus.org/how-to/globus-connect-personal-windows/)

## Submit task

Interactive access by [Open OnDemand platform](https://www.msi.umn.edu/content/using-open-ondemand)

Instruction: https://www.msi.umn.edu/support/faq/how-do-i-install-python-packages-use-jupyter-notebooks

### Slurm

#### Submit interactive job

1 apply for a node

```shell
srun -n 1 -t 24:00:00 -p agsmall,aglarge,ag2tb,msismall,msilarge,msibigmem --mem=256gb --x11 --pty bash # x11 means display GUI by default. Alternatively, you can use ssh -X cnxxx in a new tab.
srun -n 1 -t 24:00:00 -p v100 --mem=256gb --gres=gpu:v100:1 --pty bash # mangi
srun -n 1 -t 96:00:00 -p a100-4 --mem=256gb --gres=gpu:a100:1 --pty bash # agate

sinteractive -N1 -n1 --mem=230G -A agr240010 -t 96:00:00 -p shared
sinteractive -N1 -n1 --mem=256G -A agr240010 -t 48:00:00 -p highmem
# Anvil; -N: # of node; -n: # of cores
# -p: 230G for shared; highmem
# "-c" (--cpus-per-task)

sinteractive -N1 -n1 --mem=200G -A agr240010 -t 48:00:00 -p gpu --gpus-per-node=1

sinteractive -N1 -n1 --mem=200G -A agr240010 -t 48:00:00 -p gpu-debug --gpus-per-node=1
```

#### Submit job using sbatch

```shell
sbatch .bsh file # submit a job to scheduler
scancel jobID # del a job

#t GPU
#!/bin/bash -l
#SBATCH --time=2:00:00
#SBATCH --ntasks=1
#SBATCH --mail-type=FAIL
#SBATCH --mail-user=xxxxx@xxx.edu
#SBATCH -p v100
#SBATCH --gres=gpu:v100:1 
#SBATCH --mem=32gb 
#SBATCH --output=probMap10.out
#SBATCH --error=probMap10.err

#t CPU
#!/bin/bash -l
#SBATCH --time=6:00:00
#SBATCH --ntasks=1
#SBATCH --mem=64gb 
#SBATCH --mail-type=FAIL
#SBATCH --mail-user=xxxxx@xxx.edu
#SBATCH -p agsmall
#SBATCH --output=predMap1.out
#SBATCH --error=predMap1.err
#SBATCH -p amdsmall,amdlarge,ram256g,ram1t,amd512,amd2tb # for mangi
#SBATCH -p agsmall,aglarge,ag2tb,msismall,msilarge,msibigmem # for agate

cd path_exmpl
source activate env_exmpl
python script_exmple.py
```



#### Check job info

```shell
squeue --me # show only yr job info
squeue --al # show all job info
squeue --account=xxxxx
squeue -u yourUserName # show job info of this usr
squeue --user=xxxxx
sacct -j jobID # view accounting info
scontrol show job jobID # view more detailed info (usually this can be replaced by the command above
squeue --partition=v100

CF CONFIGURING: Job has been allocated resources, but are waiting for them to become ready for use (e.g. booting)
CG COMPLETING: Job is in the process of completing. Some processes on some nodes may still be active
(Resources): The job is waiting for resources to become available.
(Priority): One or more higher priority jobs exist for this partition or advanced reservation.
(ReqNodeNotAvail): Some node specifically required by the job is not currently available. it is likely to be maintained.
```



### PBS Pro

#### Submit interactive job

```shell
#t CPU
qsub -I -q casper -A UMIN0007 -l walltime=24:00:00 -l select=1:ncpus=1:mem=128GB

#t GPU
qsub -I -q casper -A UMIN0007 -l walltime=24:00:00 -l select=1:ncpus=1:mem=128GB:ngpus=1 -l gpu_type=v100
qsub -I -q casper -A UMIN0007 -l walltime=24:00:00 -l select=1:ncpus=1:mem=128GB:ngpus=2 -l gpu_type=v100
```



#### Submit job using sbatch

```shell
qsub xxx.bsh
qdel xxx.bsh

#t GPU
#!/bin/bash -l
#PBS -A UMIN0007
#PBS -l walltime=24:00:00
#PBS -l gpu_type=v100                                         
#PBS -l select=1:ncpus=1:mem=32GB:ngpus=1 // apply for cpu and gpu
#PBS -q casper

#t CPU
#!/bin/bash -l
#PBS -A UMIN0007
#PBS -l walltime=24:00:00
#PBS -l gpu_type=v100                                         
#PBS -l select=1:ncpus=1:mem=32GB // only apply for cpu
#PBS -q casper

cd /glade/work/xxxxx/UAV_cashew/0923/TRAIN_VALI_TEST
conda activate torch-env
python runPy.py > runPy.out
```

#### Check job info

```shell
qstat -a gpgpu # check who are using GPU and queuing for GPU
qstat -q gpgpu # check how many task are using GPU and queuing for GPU
qstat -u xxxxx
```

## Touch data

In scratch directory, purge policy often exists, i.e., untouched data will be removed after 90 days. You can use the following code to touch data to keep your data storaged for a longer time.

```shell
find /scratch.global/xxxxx/* -type f|xargs touch
```

## Train model using multi GPUs parallelly

```python
model = torch.nn.DataParallel(model, device_ids=[0, 1, 2, 3]) 
```

这个函数是将数据在batch维度进行分割，然后分配到不同的显卡上，对于模型的其他部分，则会在每个显卡上复制一份！具体过程如下：

1. 在前向传播过程中，各个显卡单独进行计算，每个显卡处理一部分数据；
2. 在反向传播过程中，将各个显卡计算得出的梯度进行累加即可得到最终的权重；

[【小白学深度学习-31】Pytorch多卡并行训练](https://mp.weixin.qq.com/s/gYWBAa_mRJyufLFoDTr68w)

## MSI Tier2

larger storage for inactive data

bucket name

- s3://xxxxx_planet_cashew

```shell
Create bucket (bucket is something like user directory)
s3cmd mb s3://what_you_want
i.e. >> s3cmd mb s3://taegon_bucket
// The bucket name can be between 3 and 63 characters long, and can contain only lower-case characters, numbers, periods, and dashes.

See all files stored in your account
s3cmd la

see all buckets in your account
s3cmd ls

See all files stored in your bucket
s3cmd ls s3://taegon_bucket	

Upload files into bucket
s3cmd put ~/localfile.txt s3://taegon_bucket	(upload one file)
s3cmd put * s3://taegon_bucket		(upload all files in this folder)

Download files from bucket
s3cmd get s3://taegon_bucket/localfile.txt ~/	(download one file in home)
s3cmd get s3://taegon_bucket/* .		(download all in this folder)

Check the folder size and the number of files in the folder
s3cmd du -H s3://bucket_name

Check the number of files in a folder
s3cmd ls s3://bucket_name -r | wc -l

For sharing (you need to know what their id, this is different from x.500)
This command shows your id, it would be a 5 digit number. Mine is 74747.
s3info

Then you need to update the policy for the bucket.
Write a policy file like this article. Policy filename is “s3policy-mybucket” here. You can name what you want. You can add a user id whom you want to share with.
Update policy: s3cmd setpolicy s3policy-mybucket s3://taegon_bucket
Recipients can download files using the “s3cmd get” command.

```

## Two ways to connect remote host

1 Directly use a terminal (e.g., MobaXterm) in local and using ssh

```
ssh xxxxx@agate.xxxxx.edu
```

2 NoMachine for GUI (recommend)

​	Use a terminal in msi login node. This will not close terminal even you close the webpage.

```
# online version for MSI:
nx.msi.umn.edu # input this address with connected vpn

# input the following command in login node
ssh xxxxx@agate.xxxxx.edu
```

​	It also has desktop version.

## Interactively use any cluster node with jupyterLab

1 You need a environment with ipykernal and jupyterLab in cluster

```shell
# install ipykernal
conda install -c conda-forge ipykernel

# add env to jupyter ?
python -m ipykernel install --user --name={python_env_name}

# install jupyterLab
conda install -c conda-forge jupyterLab
```



2 Run jupyterLab on cluster and connect to it from local

```shell
# Run jupyterLab server on cluster
jupyter lab --no-browser --port=6358 --ip=$(hostname)

# connect to server from local
ssh -CNL localhost:6358:{node_name}:6358 xxxxx@agate.xxxxx.edu

# open a webpage and connect to jupyterLab
localhost:6358
```





