# Moving_to_IBEX

This is intended to be a less than 30 min tutorial on how to move work environment 
from a lab node (standalone Linux) to IBEX (cluster using `slurm`to manage jobs). 

# We will cover 

1. `ssh` and `authorized_keys` for login and how to stay in the same login node if necessary
2. `/ibex/scratch/<userID>`, `scp`, `rsync`, `bquota`, `quota -s` for data transfer and disk usage 
3. `module av`, `module load`, `module unload` to check and load certain packages
4. `ginfo`, `sinfo -p batch`, `snodes` to check available resources
5. `squeue`, `srun`, `sbatch`, `debug` vs `batch` to ask a computing node and to submit jobs, and how to go to that node
6. Launch and access jupyter notebook, and custom web service
7. Overview of the IBEX documentation
8. Where to ask for help (such as installation and how to ask for extension on jobs)

# We will not cover

1. `cd`, `ls`, `vi` basic Linux command line
2. Deep learning pipeline (there are tutorials in doc). We will only cover to `import torch/tensorflow` and see if it works. 

