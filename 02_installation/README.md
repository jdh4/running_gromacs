# Installation

The installation procedure depends on the cluster.

## TigerGPU

The code can be built by following these commands:

```
$ ssh <NetID>@tigergpu.princeton.edu
$ cd </path/to/software/directory>  # e.g., cd ~/software
$ wget <https://github.com/PrincetonUniversity/running_gromacs/tree/master/02_install/tigerGpu.sh>
# make modifications to tigerGpu.sh if needed
$ bash tigerGpu.sh | tee build.log
```

For single-node jobs:

```bash
#!/bin/bash
#SBATCH --job-name=gmx           # create a short name for your job
#SBATCH --nodes=1                # node count
#SBATCH --ntasks=16              # total number of tasks across all nodes
#SBATCH --cpus-per-task=1        # cpu-cores per task (>1 if multi-threaded tasks)
#SBATCH --mem=4G                 # memory per node (4G per cpu-core is default)
#SBATCH --time=01:00:00          # total run time limit (HH:MM:SS)
#SBATCH --gres=gpu:1             # number of gpus per node
#SBATCH --mail-type=all          # send email when job begins, ends and fails
#SBATCH --mail-user=<YourNetID>@princeton.edu

module purge
module load intel/19.0/64/19.0.1.144
module load cudatoolkit/10.2

gmx grompp -f pme_verlet.mdp -c conf.gro -p topol.top -o bench.tpr
gmx mdrun -ntmpi $SLURM_NTASKS -ntomp $SLURM_CPUS_PER_TASK -s bench.tpr
```

For multi-node MPI jobs:

```bash
#!/bin/bash
#SBATCH --job-name=gmx           # create a short name for your job
#SBATCH --nodes=4                # node count
#SBATCH --ntasks-per-node=12     # total number of tasks across all nodes
#SBATCH --cpus-per-task=1        # cpu-cores per task (>1 if multi-threaded tasks)
#SBATCH --mem-per-cpu=1G         # memory per cpu-core (4G per cpu-core is default)
#SBATCH --time=01:00:00          # total run time limit (HH:MM:SS)
#SBATCH --gres=gpu:1             # number of gpus per node
#SBATCH --mail-type=all          # send email when job begins, ends and fails
#SBATCH --mail-user=<YourNetID>@princeton.edu

module purge
module load intel/19.0/64/19.0.1.144
module load cudatoolkit/10.2
module load intel-mpi/intel/2019.5/64

gmx grompp -f pme_verlet.mdp -c conf.gro -p topol.top -o bench.tpr
srun mdrun_mpi -ntomp $SLURM_CPUS_PER_TASK -s bench.tpr
```

## Traverse

Note that `rh/devtoolset/8` cannot be used to compile Gromacs.



Notes:

1. Reference Slurm variables instead of introducing duplication: `SLURM_CPUS_PER_TASK`, `SLURM_NTASKS`, `SLURM_NODES`
see `env | grep SLURM | sort`
2. Gromacs only uses serial FFT's so one does not need an MPI-enabled FFT module
3. use srun in favor of mpirun -np 16

Questions:

1. Thread MPI vs MPI for a single node
x. be sure to set `-ntmpi $SLURM_NTASKS -ntomp $SLURM_CPUS_PER_TASK`

## Della

Della is good for single node jobs. You should not be running small jobs on Tiger.

```bash
$ ssh <NetID>@della.princeton.edu
$ cd </path/to/software/directory>  # e.g., cd ~/software
$ wget <https://github.com/PrincetonUniversity/running_gromacs/tree/master/02_install/della.sh>
# make modifications to della.sh if needed
$ bash della.sh | tee build.log
```

For single-node jobs:

```bash
#!/bin/bash
#SBATCH --job-name=gmx           # create a short name for your job
#SBATCH --nodes=1                # node count
#SBATCH --ntasks=16              # total number of tasks across all nodes
#SBATCH --cpus-per-task=1        # cpu-cores per task (>1 if multi-threaded tasks)
#SBATCH --mem=4G                 # memory per node (4G per cpu-core is default)
#SBATCH --time=01:00:00          # total run time limit (HH:MM:SS)
#SBATCH --mail-type=all          # send email when job begins, ends and fails
#SBATCH --mail-user=<YourNetID>@princeton.edu
#SBATCH --exclude=della-r4c1n[1-16] # exclude ivy nodes

module purge
module load intel/19.0/64/19.0.1.144

gmx grompp -f pme_verlet.mdp -c conf.gro -p topol.top -o bench.tpr
gmx mdrun -ntmpi $SLURM_NTASKS -ntomp $SLURM_CPUS_PER_TASK -s bench.tpr
```

For multi-node MPI jobs:

```bash
#!/bin/bash
#SBATCH --job-name=gmx           # create a short name for your job
#SBATCH --nodes=4                # node count
#SBATCH --ntasks-per-node=16     # total number of tasks across all nodes
#SBATCH --cpus-per-task=1        # cpu-cores per task (>1 if multi-threaded tasks)
#SBATCH --mem-per-cpu=1G         # memory per cpu-core (4G per cpu-core is default)
#SBATCH --time=01:00:00          # total run time limit (HH:MM:SS)
#SBATCH --mail-type=all          # send email when job begins, ends and fails
#SBATCH --mail-user=<YourNetID>@princeton.edu
#SBATCH --constraint=cascade|skylake|broadwell|haswell # exclude ivy nodes

module purge
module load intel/19.0/64/19.0.1.144
module load intel-mpi/intel/2018.3/64

gmx grompp -f pme_verlet.mdp -c conf.gro -p topol.top -o bench.tpr
srun mdrun_mpi -ntomp $SLURM_CPUS_PER_TASK -s bench.tpr
```

Make two versions for Della: avx2 and avx512 if results say so;
#SBATCH --exclude=della-r4c1n[1-16]

If you ever need to run on one of the cascade lake nodes, you can create an exclusion list of all the other nodes:

```
snodes | grep -v 'cascade\|HOSTNAMES' | cut -c 1-13 | tr -s '\n' ',' | tr -d '[:blank:]' > exclude.nodes
```

## Perseus

Perseus is a lage cluster composed of Intel Broadwell CPUs. It is mainly used for astrophysics research. Run these commands to build GROMACS on perseus:

```bash
$ ssh <NetID>@tigergpu.princeton.edu
$ cd </path/to/software/directory>  # e.g., cd ~/software
$ wget <https://github.com/PrincetonUniversity/running_gromacs/tree/master/02_install/perseus.sh>
# make modifications to perseus.sh if needed
$ bash perseus.sh | tee build.log
```

Note that the latest Intel compiler cannot be used:

```
cmake fails with when using intel/19.0.5
-- Checking for 64-bit off_t
*** Error in `/opt/intel/compilers_and_libraries_2019.5.281/linux/bin/intel64/icc': free(): invalid next size (fast): 0x00002addf0000970 ***
```

## TigerCPU

## Adroit (GPU)

Gromacs can be built for GPU use on Adroit by running the following commands:

```
$ wget
$ bash adroit.sh | tee build.log
```

The `tee` command is used to have the output go to the terminal as well as a file.

The installation script is reproduced below:
