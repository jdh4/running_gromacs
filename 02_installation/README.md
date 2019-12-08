# Installation

Notes:

1. Reference Slurm variables instead of introducing duplication: `SLURM_CPUS_PER_TASK`, `SLURM_NTASKS`, `SLURM_NODES`
2. Gromacs only uses serial FFT's so one does not need an MPI-enabled FFT module
3. 

Questions:

1. Thread MPI vs MPI for a single node

## Perseus

```bash
#!/bin/bash

wget ftp://ftp.gromacs.org/pub/gromacs/gromacs-2019.4.tar.gz
tar -zxvf gromacs-2019.4.tar.gz
cd gromacs-2019.4
mkdir build_stage1
cd build_stage1

module purge
module load intel/19.0/64/19.0.1.144

OPTFLAGS="-Ofast -xHost -mtune=broadwell -DNDEBUG"

cmake3 .. -DCMAKE_BUILD_TYPE=Release \
-DCMAKE_C_COMPILER=icc -DCMAKE_C_FLAGS_RELEASE="$OPTFLAGS" \
-DCMAKE_CXX_COMPILER=icpc -DCMAKE_CXX_FLAGS_RELEASE="$OPTFLAGS" \
-DGMX_BUILD_MDRUN_ONLY=OFF -DGMX_MPI=OFF -DGMX_OPENMP=ON \
-DGMX_SIMD=AVX2_256 -DGMX_DOUBLE=OFF \
-DGMX_FFT_LIBRARY=mkl \
-DGMX_GPU=OFF \
-DCMAKE_INSTALL_PREFIX=$HOME/.local \
-DGMX_COOL_QUOTES=OFF -DREGRESSIONTEST_DOWNLOAD=ON

make -j 10
make check
make install

# starting stage 2 build of mdrun_mpi

cd ..
mkdir build_stage2
cd build_stage2

module load intel-mpi/intel/2018.3/64

cmake3 .. -DCMAKE_BUILD_TYPE=Release \
-DCMAKE_C_COMPILER=icc -DCMAKE_C_FLAGS_RELEASE="$OPTFLAGS" \
-DCMAKE_CXX_COMPILER=icpc -DCMAKE_CXX_FLAGS_RELEASE="$OPTFLAGS" \
-DGMX_BUILD_MDRUN_ONLY=ON -DGMX_MPI=ON -DGMX_OPENMP=ON \
-DGMX_SIMD=AVX2_256 -DGMX_DOUBLE=OFF \
-DGMX_FFT_LIBRARY=mkl \
-DGMX_GPU=OFF \
-DCMAKE_INSTALL_PREFIX=$HOME/.local \
-DGMX_COOL_QUOTES=OFF -DREGRESSIONTEST_DOWNLOAD=ON

make -j 10
source ../build_stage1/scripts/GMXRC
tests/regressiontests-2019.4/gmxtest.pl all
make install
```

## TigerGPU

```bash
#!/bin/bash
version=2019.4
wget ftp://ftp.gromacs.org/pub/gromacs/gromacs-${version}.tar.gz
tar -zxvf gromacs-${version}.tar.gz
cd gromacs-${version}
mkdir build_stage1
cd build_stage1

#############################################################
# starting build of gmx (stage 1)
#############################################################

module purge
module load intel/19.0/64/19.0.1.144

OPTFLAGS="-Ofast -xCORE-AVX2 -mtune=broadwell -DNDEBUG"

cmake3 .. -DCMAKE_BUILD_TYPE=Release \
-DCMAKE_C_COMPILER=icc -DCMAKE_C_FLAGS_RELEASE="$OPTFLAGS" \
-DCMAKE_CXX_COMPILER=icpc -DCMAKE_CXX_FLAGS_RELEASE="$OPTFLAGS" \
-DGMX_BUILD_MDRUN_ONLY=OFF -DGMX_MPI=OFF -DGMX_OPENMP=ON \
-DGMX_SIMD=AVX2_256 -DGMX_DOUBLE=OFF \
-DGMX_FFT_LIBRARY=mkl \
-DGMX_GPU=OFF \
-DCMAKE_INSTALL_PREFIX=$HOME/.local \
-DGMX_COOL_QUOTES=OFF -DREGRESSIONTEST_DOWNLOAD=ON

make -j 10
make check
make install

#############################################################
# starting build of mdrun_mpi (stage 2)
#############################################################

cd ..
mkdir build_stage2
cd build_stage2

module load intel-mpi/intel/2019.1/64
module load cudatoolkit
module load rh/devtoolset/7

cmake3 .. -DCMAKE_BUILD_TYPE=Release \
-DCMAKE_C_COMPILER=icc -DCMAKE_C_FLAGS_RELEASE="$OPTFLAGS" \
-DCMAKE_CXX_COMPILER=icpc -DCMAKE_CXX_FLAGS_RELEASE="$OPTFLAGS" \
-DGMX_BUILD_MDRUN_ONLY=ON -DGMX_MPI=ON -DGMX_OPENMP=ON \
-DGMX_SIMD=AVX2_256 -DGMX_DOUBLE=OFF \
-DGMX_FFT_LIBRARY=mkl \
-DGMX_GPU=ON -DGMX_CUDA_TARGET_SM=60 \
-DCMAKE_INSTALL_PREFIX=$HOME/.local \
-DGMX_COOL_QUOTES=OFF -DREGRESSIONTEST_DOWNLOAD=ON
#-DCUDA_NVCC_FLAGS_RELEASE="-ccbin=icpc -O3 --use_fast_math -arch=sm_60 --gpu-code=sm_60"

make -j 10
source ../build_stage1/scripts/GMXRC
tests/regressiontests-${version}/gmxtest.pl all
make install
```

```
#!/bin/bash
#SBATCH --job-name=gmx           # create a short name for your job
#SBATCH --nodes=1                # node count
#SBATCH --ntasks=1               # total number of tasks across all nodes
#SBATCH --cpus-per-task=1        # cpu-cores per task (>1 if multi-threaded tasks)
#SBATCH --mem-per-cpu=4G         # memory per cpu-core (4G is default)
#SBATCH --gres=gpu:1             # number of gpus per node
#SBATCH --time=00:01:00          # total run time limit (HH:MM:SS)
#SBATCH --mail-type=all          # send email on job start, end and fail
#SBATCH --mail-user=<YourNetID>@princeton.edu

module purge
module load intel/19.0/64/19.0.1.144
module load intel-mpi/intel/2019.1/64
module load cudatoolkit

export OMP_NUM_THREADS=$SLURM_CPUS_PER_TASK
export GMX_MAXBACKUP=-1

BCH=../gpu_bench/rnase_cubic
srun gmx grompp -f $BCH/pme_verlet.mdp -c $BCH/conf.gro -p $BCH/topol.top -o bench.tpr
srun mdrun_mpi -ntomp $SLURM_CPUS_PER_TASK -s bench.tpr -c conf.gro
```

## Traverse

Note that `rh/devtoolset/8` cannot be used to compile Gromacs.

#### FFTW

```
#!/bin/bash

#############################################################
# build a fast version FFTW
#############################################################

version=3.3.8
wget ftp://ftp.fftw.org/pub/fftw/fftw-${version}.tar.gz
tar -zxvf fftw-${version}.tar.gz
cd fftw-${version}

module purge
module load rh/devtoolset/8

./configure CC=gcc CFLAGS="-Ofast -mcpu=power9 -mtune=power9 -DNDEBUG" --prefix=$HOME/.local \
--enable-shared --enable-single --enable-vsx --disable-fortran

make
make install
cd ..

#############################################################
# starting build of gmx (stage 1)
#############################################################

version=2019.4
wget ftp://ftp.gromacs.org/pub/gromacs/gromacs-${version}.tar.gz
tar -zxvf gromacs-${version}.tar.gz
cd gromacs-${version}
mkdir build_stage1
cd build_stage1

module purge
module load rh/devtoolset/7

OPTFLAGS="-Ofast -mcpu=power9 -mtune=power9 -DNDEBUG"

cmake3 .. -DCMAKE_BUILD_TYPE=Release \
-DCMAKE_C_COMPILER=gcc -DCMAKE_C_FLAGS_RELEASE="$OPTFLAGS" \
-DCMAKE_CXX_COMPILER=g++ -DCMAKE_CXX_FLAGS_RELEASE="$OPTFLAGS" \
-DGMX_BUILD_MDRUN_ONLY=OFF -DGMX_MPI=OFF -DGMX_OPENMP=ON \
-DGMX_SIMD=IBM_VSX -DGMX_DOUBLE=OFF \
-DGMX_FFT_LIBRARY=fftw3 \
-DFFTWF_LIBRARY=$HOME/.local/include \
-DFFTWF_LIBRARY=$HOME/.local/lib/libfftw3f.so \
-DGMX_GPU=OFF \
-DCMAKE_INSTALL_PREFIX=$HOME/.local \
-DGMX_COOL_QUOTES=OFF -DREGRESSIONTEST_DOWNLOAD=ON

make -j 10
make check
make install

#############################################################
# starting build of mdrun_mpi (stage 2)
#############################################################

cd ..
mkdir build_stage2
cd build_stage2

module load openmpi/devtoolset-8/4.0.1/64
module load cudatoolkit

cmake3 .. -DCMAKE_BUILD_TYPE=Release \
-DCMAKE_C_COMPILER=gcc -DCMAKE_C_FLAGS_RELEASE="$OPTFLAGS" \
-DCMAKE_CXX_COMPILER=g++ -DCMAKE_CXX_FLAGS_RELEASE="$OPTFLAGS" \
-DGMX_BUILD_MDRUN_ONLY=ON -DGMX_MPI=ON -DGMX_OPENMP=ON \
-DGMX_SIMD=IBM_VSX -DGMX_DOUBLE=OFF \
-DGMX_FFT_LIBRARY=fftw3 \
-DFFTWF_LIBRARY=$HOME/.local/include \
-DFFTWF_LIBRARY=$HOME/.local/lib/libfftw3f.so \
-DGMX_GPU=ON -DGMX_CUDA_TARGET_SM=70 \
-DCMAKE_INSTALL_PREFIX=$HOME/.local \
-DGMX_COOL_QUOTES=OFF -DREGRESSIONTEST_DOWNLOAD=ON

make -j 10
source ../build_stage1/scripts/GMXRC
tests/regressiontests-${version}/gmxtest.pl all
make install
```

Step 1

```
wget http://ftp.gromacs.org/pub/gromacs/gromacs-5.1.4.tar.gz
tar -xvf gromacs-5.1.4.tar.gz
cd gromacs-5.1.4
mkdir build
cd build

module purge
module load fftw/gcc/3.3.8

cmake .. -DGMX_FFT_LIBRARY=fftw3 -DGMX_BUILD_OWN_FFTW=OFF -DCMAKE_INCLUDE_PATH=/usr/local/fftw/gcc/3.3.8/include -DCMAKE_LIBRARY_PATH=/usr/local/fftw/gcc/3.3.8/lib64 -DGMX_MPI=OFF -DGMX_GPU=OFF -DGMX_DOUBLE=OFF -DGMX_SIMD=IBM_VSX -DBUILD_SHARED_LIBS=OFF -DCMAKE_BUILD_TYPE=Release -DCMAKE_C_COMPILER=xlc -DCMAKE_C_FLAGS_RELEASE="-O3 -qarch=pwr9 -qtune=pwr9 -DNDEBUG" -DCMAKE_CXX_COMPILER=xlC -DCMAKE_CXX_FLAGS_RELEASE="-O3 -qarch=pwr9 -qtune=pwr9 -DNDEBUG" -DGMX_COOL_QUOTES=OFF -DREGRESSIONTEST_DOWNLOAD=ON -DCMAKE_INSTALL_PREFIX=~/.local

make
make check
make install
```

Step 2

```
module load openmpi/
fftw/gcc/3.3.8         2) openmpi/gcc/4.0.1/64   3) cudatoolkit/10.1

cmake .. -DGMX_BUILD_MDRUN_ONLY=ON -DGMX_FFT_LIBRARY=fftw3 -DGMX_BUILD_OWN_FFTW=OFF -DCMAKE_INCLUDE_PATH=/usr/local/fftw/gcc/3.3.8/include -DCMAKE_LIBRARY_PATH=/usr/local/fftw/gcc/3.3.8/lib64 -DGMX_MPI=ON -DGMX_GPU=ON -DCUDA_TOOLKIT_ROOT_DIR=/usr/local/cuda-10.0 -DGMX_CUDA_TARGET_SM=70 -DGMX_CUDA_TARGET_COMPUTE=70 -DGMX_OPENMP=ON -DGMX_DOUBLE=OFF -DGMX_SIMD=IBM_VSX -DBUILD_SHARED_LIBS=OFF -DCMAKE_BUILD_TYPE=Release -DCMAKE_C_COMPILER=xlc -DCMAKE_C_FLAGS_RELEASE="-O3 -qarch=pwr9 -qtune=pwr9 -DNDEBUG" -DCMAKE_CXX_COMPILER=xlC -DCMAKE_CXX_FLAGS_RELEASE="-O3 -qarch=pwr9 -qtune=pwr9 -DNDEBUG" -DGMX_COOL_QUOTES=OFF -DREGRESSIONTEST_DOWNLOAD=ON -DCMAKE_INSTALL_PREFIX=~/.local
```

## old Perseus

```
cmake fails with when using intel/19.0.5
-- Checking for 64-bit off_t
*** Error in `/opt/intel/compilers_and_libraries_2019.5.281/linux/bin/intel64/icc': free(): invalid next size (fast): 0x00002addf0000970 ***
```


```
For test suite output see /build_info

The building is done in a two stage process. The serial
components are built in the first stage. The mdrun_mpi
executable is built in the second stage.

#############################################################
$ cd /home/<NetID>/software
$ wget https://github.com/PrincetonUniversity/running_gromacs/tree/master/02_install/perseus.sh
$ bash perseus.sh | tee output.log



#!/bin/bash
wget ftp://ftp.gromacs.org/pub/gromacs/gromacs-2019.4.tar.gz
tar -zxvf gromacs-2019.4.tar.gz
cd gromacs-2019.4
mkdir build
cd build

module purge
module load intel/19.0/64/19.0.1.144

cmake3 .. -DGMX_FFT_LIBRARY=mkl -DGMX_MPI=OFF -DGMX_SIMD=AVX2_256 -DGMX_GPU=OFF -DCMAKE_BUILD_TYPE=Release \
-DCMAKE_C_COMPILER=icc -DCMAKE_C_FLAGS_RELEASE="-xHost -O3 -DNDEBUG" -DCMAKE_CXX_COMPILER=icpc \
-DCMAKE_CXX_FLAGS_RELEASE="-O3 -xHost -mtune=broadwell -DNDEBUG" -DGMX_COOL_QUOTES=OFF -DREGRESSIONTEST_DOWNLOAD=ON \
-DCMAKE_INSTALL_PREFIX=$HOME/.local

make -j 10
make check
make install

module load intel-mpi/intel/2018.3/64

mk build_stage2_parallel
cmake3 .. -DGMX_FFT_LIBRARY=mkl -DGMX_MPI=ON -DGMX_SIMD=AVX2_256 -DGMX_GPU=OFF -DCMAKE_BUILD_TYPE=Release
-DCMAKE_C_COMPILER=icc -DCMAKE_C_FLAGS_RELEASE="-xHost -O3 -DNDEBUG" -DCMAKE_CXX_COMPILER=icpc
-DCMAKE_CXX_FLAGS_RELEASE="-xHost -O3 -DNDEBUG" -DGMX_COOL_QUOTES=OFF -DREGRESSIONTEST_DOWNLOAD=ON
-DGMX_BUILD_MDRUN_ONLY=ON -DCMAKE_INSTALL_PREFIX=/projects/SOFTWARE/gromacs/perseus-serial-parallel/2019.2

make -j 10
source ../build_stage1_serial/scripts/GMXRC.bash 
tests/regressiontests-2019.2/gmxtest.pl all
make install

###########################################################
#  Example Slurm script for Perseus
###########################################################
#!/bin/bash
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=8
#SBATCH --time=00:05:00

module purge
module load gromacs/2019.2

srun gmx grompp -f npt_md.mdp -c methane_start.pdb -p methane.top -o methane_npt.tpr
srun mdrun_mpi -s methane_npt.tpr -v -c methane_npt.gro
###########################################################

```

## TigerCPU

## Adroit

```
```
