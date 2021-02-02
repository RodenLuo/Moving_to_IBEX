# Moving_to_IBEX

This is intended to be a less than 30 min tutorial on how to move work environment 
from a lab node (standalone Linux) to IBEX (cluster using `slurm`to manage jobs). 

The basic idea is if you know how to run your tasks on a standalone Linux machine, 
and feel there is a learning curve for using IBEX and hence hesitated, 
then after this tutorial, you should feel very comfortable to use IBEX. 

# We will cover 

1. `ssh` and `authorized_keys` for login and how to stay in the same login node if necessary
2. `/ibex/scratch/<userID>`, `scp`, `rsync`, `bquota`, `quota -s` for data transfer and disk usage 
3. `module av`, `module load`, `module unload` to check and load certain packages
4. `ginfo`, `sinfo -p batch`, `snodes` to check available resources
5. `squeue`, `srun`, `sbatch`, `debug` vs `batch` to ask a computing node and to submit jobs, check `.out` and `.err`, and how to go to that node to check usage stats. 
6. Launch and access jupyter notebook, and custom web service
7. Overview of the IBEX documentation
8. Where to ask for help (such as installation and how to ask for extension on jobs)
9. `tmux` for remote shell
10. `imgcat` the shell integration tool to check images inside the iTerm commandline window.

# We will not cover

1. `cd`, `ls`, `vi` basic Linux command line
2. Deep learning pipeline (there are tutorials in doc). We will only cover to `import torch/tensorflow` and see if it works. 

## IBEX architecture 

[![Image from Gyazo](https://i.gyazo.com/6c4e09c97b03f8ae4915e5514d5845f1.png)](https://gyazo.com/6c4e09c97b03f8ae4915e5514d5845f1)

## 1. `ssh` and `authorized_keys` for login and how to stay in the same login node if necessary


```shell
# client
cat ~/.ssh/id_rsa.pub| pbcopy
ssh <portal_ID>@glogin.ibex.kaust.edu.sa
```

```shell
# server
vi ~/.ssh/authorized_keys
```

Check out [bash: /home/user/.ssh/authorized_keys: No such file or directory](https://askubuntu.com/questions/466549/bash-home-user-ssh-authorized-keys-no-such-file-or-directory) if you creat the file for the first time. 

```shell
# stay in the same login node
(base) [luod@login510-22 ~]$ which glogin
~/.myscript/glogin
(base) [luod@login510-22 ~]$ cat ~/.myscript/glogin
#!/bin/bash
ssh login510-27
(base) [luod@login510-22 ~]$ glogin
Last login: Sun Jan 31 17:00:01 2021 from 10.240.0.57
/-------------------------** IBEX Support **--------------16-12-2020-\
|                                                                    |
| For help with Ibex use either:                                     |
|                                                                    |
|    http://hpc.kaust.edu.sa/ibex - Wiki containing ibex information |
|                                                                    |
|    http://kaust-ibex.slack.com - use #general for simple queries   |
|                                                                    |
|    ibex@hpc.kaust.edu.sa - to create a request ticket              |
|                                                                    |
\--------------------------------------------------------------------/
(base) [luod@login510-27 ~]$
```


## 2. `/ibex/scratch/<userID>`, `scp`, `rsync`, `bquota`, `quota -s` for data transfer and disk usage 

```shell
# server
(base) [luod@login510-27 ~]$ pwd
/home/luod
(base) [luod@login510-27 ~]$ which cds
alias cds='cd /ibex/scratch/luod'
	/usr/bin/cd
(base) [luod@login510-27 ~]$ cds
(base) [luod@login510-27 luod]$ pwd
/ibex/scratch/luod
```

```shell
# client
scp output.* luod@glogin.ibex.kaust.edu.sa:/ibex/scratch/luod/oxDNA_tutorial/oxDNA_tutorial_input_files_sbatch/tetraBrick_4_vertices/987/add_7/

# Make or update a remote location to be an exact copy of the source:
rsync -av --progress --delete /path/to/localdir/ USER@HOSTNAME:/path/to/destination
```

Check IBEX doc [here](https://www.hpc.kaust.edu.sa/ibex/transfering_data) for `rsync`.

```shell
(base) [luod@login510-27 ~]$ bquota # check scratch disk usage
Quota information for IBEX filesystems:
  Scratch             (/ibex/scratch):     Used:      392.90 GB   Limit:     1536.00 GB
(base) [luod@login510-27 ~]$ quota -s # check home dir usage
```


## 3. `module av`, `module load`, `module unload` to check and load certain packages

```shell
(base) [luod@login510-27 ~]$ module av pytorch

------------------------------------- /sw/csgv/modulefiles/applications -------------------------------------
pytorch/1.0.1-cuda10.0-cudnn7.6-py3.6 pytorch/1.2.0-cuda10.0-cudnn7.6-py3.7
(base) [luod@login510-27 ~]$ module av tensorflow

------------------------------------- /sw/csgv/modulefiles/applications -------------------------------------
tensorflow/1.11.0                         tensorflow/1.14.0(default)
tensorflow/1.11.0-cuda9.2-cudnn7.2-py3.6  tensorflow/1.14.0-cuda10.0-cudnn7.6-py3.7
tensorflow/1.13.1                         tensorflow/2.0.0
tensorflow/1.13.1-cuda10.0-cudnn7.6-py3.6 tensorflow/2.0.0-cuda10.0-cudnn7.6-py3.7
(base) [luod@login510-27 ~]$ module av cuda

-------------------------------------- /sw/csgv/modulefiles/compilers ---------------------------------------
cuda/10.0.130          cuda/10.1.243(default) cuda/11.0.1            cuda/9.2.148.1
cuda/10.1.105          cuda/10.2.89           cuda/9.0.176


(base) [luod@login510-27 ~]$ module load pytorch/1.2.0-cuda10.0-cudnn7.6-py3.7
(base) [luod@login510-27 ~]$ python
Python 3.7.4 (default, Aug 13 2019, 20:35:49)
[GCC 7.3.0] :: Anaconda, Inc. on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> import torch
>>> print(torch.__version__)
1.2.0


(base) [luod@login510-27 ~]$ module unload pytorch/1.2.0-cuda10.0-cudnn7.6-py3.7
Unloading module for Anaconda
Anaconda 4.4.0 is now unloaded
GNU 6.4.0 is now unloaded
Unloading module for Machine Learning 2019.02
Machine Learning 2019.02 is now unloaded
(base) [luod@login510-27 ~]$ python
Python 2.7.5 (default, Nov 16 2020, 22:23:17)
[GCC 4.8.5 20150623 (Red Hat 4.8.5-44)] on linux2
Type "help", "copyright", "credits" or "license" for more information.
>>> import torch
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ImportError: No module named torch
```

## 4. `ginfo`, `sinfo -p batch`, `snodes` to check available resources

```shell
(base) [luod@login510-27 ~]$ ginfo

GPUS currently in use:
GPU Models:          Total  Used  Free
gtx1080ti               64    13    51
p100                    12     5     7
p6000                    4     0     4
rtx2080ti               32    11    21
v100                   274   241    33
              Total:   386   270   116
```

## 5. `squeue`, `srun`, `sbatch`, `debug` vs `batch` to ask a computing node and to submit jobs, check `.out` and `.err`, and how to go to that node to check usage stats.

```shell
(base) [luod@login510-22 ~]$ myq
             JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
          13912127      gpu4 plane_1X     luod  R      51:29      1 gpu211-10
(base) [luod@login510-22 ~]$ which myq
alias myq='squeue -u luod'
	/opt/slurm/cluster/ibex/install/bin/squeue
(base) [luod@login510-22 ~]$ which gpu-4h-bash
alias gpu-4h-bash='srun -p batch --pty --time=4:00:00 --constraint=[gpu] --cpus-per-task=1 --gres=gpu:1 bash -l'
	/opt/slurm/cluster/ibex/install/bin/srun
(base) [luod@login510-22 ~]$ which v100-4h-bash
alias v100-4h-bash='srun -p batch --pty --time=4:00:00 --constraint=[gpu] --cpus-per-task=1 --gres=gpu:v100:1 bash -l'
	/opt/slurm/cluster/ibex/install/bin/srun
(base) [luod@login510-22 ~]$ gpu-4h-bash
srun: job 13912529 queued and waiting for resources
srun: job 13912529 has been allocated resources
(base) [luod@gpu211-06 ~]$ nvidia-smi
```

check [here](https://www.hpc.kaust.edu.sa/ibex/job) for IBEX job generator, and [here](https://www.hpc.kaust.edu.sa/ibex/slurm_constraints) for more srun commands.

```shell
(base) [luod@login510-22 test]$ ls
run_all.sh
(base) [luod@login510-22 test]$ cat run_all.sh
#!/bin/bash
#SBATCH -N 1
#SBATCH --partition=batch
#SBATCH -J test
#SBATCH -o test.%J.out
#SBATCH -e test.%J.err
#SBATCH --mail-user=deng.luo@kaust.edu.sa
#SBATCH --mail-type=ALL
#SBATCH --time=4:00:00
#SBATCH --mem=128G
#SBATCH --gres=gpu:1
#SBATCH --cpus-per-task=6
#SBATCH --constraint=[gpu]

#run the application:
now=$(date +"%T")
echo "Current time : $now"

sleep 120

now=$(date +"%T")
echo "Current time : $now"

(base) [luod@login510-22 test]$ sbatch run_all.sh
Submitted batch job 13912810
(base) [luod@login510-22 test]$ myq
             JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
          13912810      gpu4     test     luod  R       0:01      1 gpu212-10
(base) [luod@login510-22 test]$ which showJob
~/.myscript/showJob
(base) [luod@login510-22 test]$ cat ~/.myscript/showJob
#!/bin/bash

if [[ $# -eq 0 ]] ; then
    echo 'usage: showJob <SLURM_JOB_ID>'
    exit 0
fi

SLURM_JOB_ID=$1

scontrol show jobid -dd $SLURM_JOB_ID
(base) [luod@login510-22 test]$ showJob 13912810

... 
...
...

(base) [luod@login510-22 test]$ which gotoGPUJob
~/.myscript/gotoGPUJob
(base) [luod@login510-22 test]$ cat ~/.myscript/gotoGPUJob
#!/bin/bash

if [[ $# -eq 0 ]] ; then
    echo 'usage: gotoGPUJob <SLURM_JOB_ID> [NUMBER_OF_GPUS=1]'
    exit 0
fi

SLURM_JOB_ID=$1

if [ -z "$2" ]; then
    NUMBER_OF_GPUS=1
else
    NUMBER_OF_GPUS=$2
fi

srun --jobid $SLURM_JOB_ID --gpus=$NUMBER_OF_GPUS --pty /bin/bash --login
(base) [luod@login510-22 test]$ gotoGPUJob 13912810
(base) [luod@gpu212-10 test]$ nvidia-smi
...
...
...
(base) [luod@gpu212-10 test]$ logout
(base) [luod@login510-22 test]$ ls
run_all.sh  test.13912810.err  test.13912810.out
(base) [luod@login510-22 test]$ myq
             JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
          13912810      gpu4     test     luod  R       1:01      1 gpu212-10
(base) [luod@login510-22 test]$ less test.13912810.out
(base) [luod@login510-22 test]$ less test.13912810.err
```

Check Email for `BEGIN`, `COMPLETED`, `FAILED`, `CANCELED`, notifications





