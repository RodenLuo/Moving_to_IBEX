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
