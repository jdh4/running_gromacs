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

The shared library dependencies of `gmx`:

```bash
$ ldd gmx
linux-vdso.so.1 =>  (0x00007ffc2cbfe000)
libmkl_intel_lp64.so => /opt/intel/compilers_and_libraries_2019.1.144/linux/mkl/lib/intel64/libmkl_intel_lp64.so (0x00002abff6918000)
libmkl_sequential.so => /opt/intel/compilers_and_libraries_2019.1.144/linux/mkl/lib/intel64/libmkl_sequential.so (0x00002abff7466000)
libmkl_core.so => /opt/intel/compilers_and_libraries_2019.1.144/linux/mkl/lib/intel64/libmkl_core.so (0x00002abff8a12000)
libgromacs_gpu.so.4 => /home/jdh4/.local/bin/./../lib64/libgromacs_gpu.so.4 (0x00002abffcb9e000)
libm.so.6 => /lib64/libm.so.6 (0x00002abffe3a8000)
libstdc++.so.6 => /lib64/libstdc++.so.6 (0x00002abffe6aa000)
libiomp5.so => /opt/intel/compilers_and_libraries_2019.1.144/linux/compiler/lib/intel64/libiomp5.so (0x00002abffe9b2000)
libgcc_s.so.1 => /lib64/libgcc_s.so.1 (0x00002abffed9a000)
libpthread.so.0 => /lib64/libpthread.so.0 (0x00002abffefb0000)
libc.so.6 => /lib64/libc.so.6 (0x00002abfff1cc000)
libdl.so.2 => /lib64/libdl.so.2 (0x00002abfff59a000)
librt.so.1 => /lib64/librt.so.1 (0x00002abfff79e000)
libcufft.so.10 => /usr/local/cuda-10.2/lib64/libcufft.so.10 (0x00002abfff9a6000)
libhwloc.so.5 => /lib64/libhwloc.so.5 (0x00002ac008e46000)
libimf.so => /opt/intel/compilers_and_libraries_2019.1.144/linux/compiler/lib/intel64/libimf.so (0x00002ac009083000)
libsvml.so => /opt/intel/compilers_and_libraries_2019.1.144/linux/compiler/lib/intel64/libsvml.so (0x00002ac009623000)
libirng.so => /opt/intel/compilers_and_libraries_2019.1.144/linux/compiler/lib/intel64/libirng.so (0x00002ac00afc6000)
libintlc.so.5 => /opt/intel/compilers_and_libraries_2019.1.144/linux/compiler/lib/intel64/libintlc.so.5 (0x00002ac00b338000)
/lib64/ld-linux-x86-64.so.2 (0x00002abff66f4000)
libnuma.so.1 => /lib64/libnuma.so.1 (0x00002ac00b5aa000)
libltdl.so.7 => /lib64/libltdl.so.7 (0x00002ac00b7b5000)
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

The shared library dependencies of `mdrun_mpi` are:

```bash
$ ldd mdrun_mpi
linux-vdso.so.1 =>  (0x00007fff8e143000)
libmkl_intel_lp64.so => /opt/intel/compilers_and_libraries_2019.1.144/linux/mkl/lib/intel64/libmkl_intel_lp64.so (0x00002adc6a709000)
libmkl_sequential.so => /opt/intel/compilers_and_libraries_2019.1.144/linux/mkl/lib/intel64/libmkl_sequential.so (0x00002adc6b257000)
libmkl_core.so => /opt/intel/compilers_and_libraries_2019.1.144/linux/mkl/lib/intel64/libmkl_core.so (0x00002adc6c803000)
libmpifort.so.12 => /opt/intel/compilers_and_libraries_2019.5.281/linux/mpi/intel64/lib/libmpifort.so.12 (0x00002adc7098f000)
libmpi.so.12 => /opt/intel/compilers_and_libraries_2019.5.281/linux/mpi/intel64/lib/release/libmpi.so.12 (0x00002adc70d4d000)
libdl.so.2 => /lib64/libdl.so.2 (0x00002adc71d52000)
librt.so.1 => /lib64/librt.so.1 (0x00002adc71f56000)
libpthread.so.0 => /lib64/libpthread.so.0 (0x00002adc7215e000)
libcufft.so.10 => /usr/local/cuda-10.2/lib64/libcufft.so.10 (0x00002adc7237a000)
libhwloc.so.5 => /lib64/libhwloc.so.5 (0x00002adc7b81a000)
libm.so.6 => /lib64/libm.so.6 (0x00002adc7ba57000)
libstdc++.so.6 => /lib64/libstdc++.so.6 (0x00002adc7bd59000)
libiomp5.so => /opt/intel/compilers_and_libraries_2019.1.144/linux/compiler/lib/intel64/libiomp5.so (0x00002adc7c061000)
libgcc_s.so.1 => /lib64/libgcc_s.so.1 (0x00002adc7c449000)
libc.so.6 => /lib64/libc.so.6 (0x00002adc7c65f000)
/lib64/ld-linux-x86-64.so.2 (0x00002adc6a4e5000)
libfabric.so.1 => /opt/intel/compilers_and_libraries_2019.5.281/linux/mpi/intel64/libfabric/lib/libfabric.so.1 (0x00002adc7ca2d000)
libnuma.so.1 => /lib64/libnuma.so.1 (0x00002adc7cc65000)
libltdl.so.7 => /lib64/libltdl.so.7 (0x00002adc7ce70000)
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
