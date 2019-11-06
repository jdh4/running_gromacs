# Running Gromacs

This guide presents best practices with Gromacs on the Princeton HPC.

## TigerGPU

```
#!/bin/bash
wget ftp://ftp.gromacs.org/pub/gromacs/gromacs-2019.2.tar.gz
tar xf gromacs-2019.2.tar.gz
cd gromacs-2019.2
mkdir build
cd build

module purge
module load intel/19.0/64/19.0.3.199
module load intel-mpi/intel/2018.3/64
module load cudatoolkit/10.1
module load rh/devtoolset/7

cmake3 .. -DGMX_FFT_LIBRARY=mkl -DGMX_MPI=ON -DGMX_DOUBLE=OFF -DGMX_SIMD=AVX2_256 -DREGRESSIONTEST_DOWNLOAD=ON -DCMAKE_C_COMPILER=icc -DCMAKE_C_FLAGS="-xHost -O3 -DNDEBUG" -DCMAKE_CXX_COMPILER=icpc -DCMAKE_CXX_FLAGS="-xHost -O3 -DNDEBUG" -DGMX_GPU=ON -DGMX_CUDA_TARGET_SM=60 -DGMX_COOL_QUOTES=OFF -DCMAKE_INSTALL_PREFIX=/projects/SOFTWARE/gromacs/tigerGpu-intel-intelmpi-cuda/2019.2

make -j 10
make check
make install
```

```
#!/bin/bash
#SBATCH --job-name=cuda_c        # create a short name for your job
#SBATCH --nodes=1                # node count
#SBATCH --ntasks=14              # total number of tasks across all nodes
#SBATCH --cpus-per-task=1        # cpu-cores per task (>1 if multi-threaded tasks)
#SBATCH --mem-per-cpu=4G         # memory per cpu-core (4G is default)
#SBATCH --gres=gpu:2             # number of gpus per node
#SBATCH --time=00:01:00          # total run time limit (HH:MM:SS)
#SBATCH --mail-type=begin        # send email when job begins
#SBATCH --mail-type=end          # send email when job ends
#SBATCH --mail-user=<YourNetID>@princeton.edu

module purge
module load gromacs/tigerGpu/parallel/2019.2

srun gmx_mpi grompp -f npt_md.mdp -c methane_start.pdb -p methane.top -o methane_npt.tpr
srun gmx_mpi mdrun -s methane_npt.tpr -v -c methane_npt.gro
```
