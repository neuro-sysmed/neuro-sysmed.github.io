# Neuro-SysMed Azure Infrastructure

The neuro-sysmed azure infrastructure isolates each group with a shared HPC resource. Each group has a dedicated virtual server (VM) for minor work (name: *project*-linux?)


## Storage

Each user have a limited home space for personal code and results, but should not be used for project/group results.

The generic structure for data follows this naming template: /data/**group**/**project**/**experiment**/.  How the data is organised in each exeriment is free? for the lead researcher. However it is recommended that a raw/input directory with strict restrictions exists to ensure data is not accidentially deleted.

**Note**: The projects directories are loaded on demand, so might not be visible prior to accessing the resource. /data/**group**/README lists all the projects for a given group.

Temporary research data should be stored in /scratch/**group**/. Here each user have a dedicated directory, it is possible to make project/experiment directories if multiple researchers are collaborating closely.



|Path                | Filesystem  |Approximate Size  | Backup | Comments                                        |
|  ~/                | NFS4        | 20GB/user        | Yes    | Personal code and results                       |
|/data/**group**/REC | NFS3        |  5BP             | Yes    | Raw and final results for research              |
|/scratch/**group**/ | NFS3        |  5BP             | No     | For temporary storage of data for active jobs   |


## Slurm 

All groups have access to a slurm instance. There are 4 different queues defined:


| Queue         | CPUS   | RAM   | Instances |
| ------------- | ------:| -----:| ---------:|
| small         | 2      |     8 | 10        |
| medium        | 4      |    16 | 10        |
| large         | 8      |    32 |  2        |
| huge          | 32     |    64 |  2        |


```
# See HPC status 
sinfo

# Submit a job 
sbatch hpc_job.sh

# Oneliner:
  sbatch  -p small --wrap "samtools index sample.bam "

# list job queue
squeue

# detailed job info
sacct

# Job information 
scontol show job <JOBID>


```


And example sbatch file:

```
#!/bin/bash
#SBATCH --job-name=test_job
#SBATCH --time=2:00                     # max runtime
#SBATCH --nodes=1 --ntasks-per-node=1   # resources
#SBATCH --cpus-per-task                 # CPUs/task
#SBATCH --mem=32g                       # mem requirement
#SBATCH --output=slurm-%j.out           # Output file for this job.    
#SBATCH --error slurm-%j.err            # Error file for this job.
#SBATCH --partition=small               # Partition to use
#SBATCH -o slurm-%j.out

date
sleep 60
hostname
date

```

It is possible to change outfiles names (as show above) in various ways eg:

+ **%j** : jobid of the running job.
+ **%J** : jobid.stepid of the running job. (e.g. "128.0")
+ **%s** : stepid of the running job.
+ **%u** : User name.
+ **%x** : Job name.



For all options see here: https://slurm.schedmd.com/sbatch.html


## Modules (lmod)

To provide different version of research software we use the modules environment extensively. Loading a module establishes the environment required to find the related include and library files at compile-time and run-time.

By default the environment is such that the most commonly required modules are already loaded, however it is possible to configure a default user environment or even a project specific environment.


```
# list modules available
module avail

# load module default version
module load samtools

# load  module spec version 
module load samtools/1.11

# save module env (default), will be reloaded on login
module save


# save, and load module env (default)
module save project1
module loadsave project1


# load shared environment
module load StdEnv # default for all users 

```