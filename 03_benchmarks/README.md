# Benchmarks

GPU benchmark input files from [this page](http://www.gromacs.org/GPU_acceleration):

```
$ wget ftp://ftp.gromacs.org/pub/benchmarks/rnase_bench_systems.tar.gz
$ tar -zxvf rnase_bench_systems.tar.gz
$ ls -l
total 1536
-rw-r--r--. 1 jdh4 cses 1571724 Dec  8 11:36 rnase_bench_systems.tar.gz
drwxr-xr-x. 2 jdh4 cses     102 Dec  8 11:37 rnase_cubic
drwxr-xr-x. 2 jdh4 cses     102 Dec  8 11:37 rnase_dodec
drwxr-xr-x. 2 jdh4 cses     116 Dec  8 11:37 rnase_dodec_vsites

$ wget ftp://ftp.gromacs.org/pub/benchmarks/ADH_bench_systems.tar.gz
```

Here we use cubic for larger systems should use octa. Using h-bonds instead of all-bonds constraints.

## ADH with cubic box (single and multi-node)

| cluster      | wall time (s) | ns /day |  nodes   | ntasks-per-node |  cpus-per-task    | total cores | GPUs  |
|:-------------|-------------:|---------:|:--------:|:---------------:|:-----------------:|:-----------:|:-----:|
| tigerCpu     |   383.5      |  4.5     |   1      |  1              |        2          |    2        | 0     |
| tigerCpu     |   211.6      |  8.2     |   1      |  1              |        4          |    4        | 0     |
| tigerCpu     |   209.9      |  8.2     |   1      |  4              |        1          |    4        | 0     |
| tigerCpu     |   168.4      | 10.2     |   1      |  8              |        1          |    8        | 0     |
| tigerCpu (6) |   128.2      | 13.4     |   1      |  8              |        1          |    8        | 0     |
| tigerCpu     |    88.1      | 19.6     |   1      |  16             |        1          |    16       | 0     |
| tigerCpu (6) |    89.5      | 19.3     |   1      |  16             |        1          |    16       | 0     |
| tigerCpu     |   137.1      | 12.6     |   1      | 10              |        1          |   10        | 0     |
| tigerCpu     |    80.8      | 21.4     |   2      | 10              |        1          |   20        | 0     |
| tigerCpu     |    41.9      | 41.2     |   4      | 10              |        1          |   40        | 0     |
| tigerCpu     |    24.5      | 70.5     |   4      | 20              |        1          |   80        | 0     |
| tigerCpu     |    25.7      | 67.3     |   2      | 40              |        1          |   80        | 0     |
| tigerCpu     |    44.8      | 38.6     |   4      |  4              |        5          |   80        | 0     |
| tigerCpu     |    29.8      | 57.9     |   4      | 10              |        2          |   80        | 0     |
| tigerCpu     |    29.8      | 57.9     |   4      | 10              |        2          |   80        | 0     |
| tigerCpu     |    24.6      | 70.2     |   8      | 10              |        1          |   80        | 0     |
| traverse*    |    125.3     | 13.8     |   1      | 1               |        1          |   1         | 1     |
| traverse*    |     48.8     | 35.4     |   1      | 16              |        1          |   1         | 1     |
| traverse*    |     30.0     | 57.6     |   1      | 1               |        16         |   16        | 1     |
| traverse*    |     18.4     | 94.0     |   1      | 1               |        32         |   32        | 1     |
| traverse*    |     66.7     | 25.9     |   2      | 16              |        1          |   32        | 1     |
| traverse*    |     79.1     | 21.8     |   2      | 32              |        1          |   64        | 1     |
| traverse*    |     40.5     | 42.6     |   2      | 16              |        2          |   32        | 1     |

(6) using gmx_cpu mdrun instead of mdrun_cpu_mpi

## RNASE with cubic box (single node)

| cluster               | wall time (s)  | ns/day   |  ntasks  |  cpus-per-task  |  threads-per-core | total cores |  GPUs  |
|:----------------------|----------:|--------------:|:--------:|:---------------:|:-----------------:|:-----------:|:-----:|
| adroit (v100)         |    10.5   | 163.9         |   1      | 1               |        1          |   1         | 1     |
| adroit (v100)         |    18.6   |  93.1         |   2      | 1               |        1          |   2         | 1     |
| adroit (v100)         |    12.3   | 140.0         |   4      | 1               |        1          |   4         | 1     |
| adroit (v100)         |    19.0   |  90.8         |   8      | 1               |        1          |   8         | 1     |
| adroit (v100)         |    18.6   | 156.1         |   4      | 2               |        1          |   8         | 1     |
| adroit (v100)         |    10.7   | 161.2         |   4      | 4               |        1          |   16        | 1     |
| adroit (v100)         |     6.5   | 263.4         |   1      | 2               |        1          |   2         | 1     |
| adroit (v100)         |     4.9   | 353.6         |   1      | 4               |        1          |   4         | 1     |
| adroit (v100)         |     4.3   | 399.0         |   1      | 8               |        1          |   8         | 1     |
| adroit (v100)         |     4.3   | 400.4         |   1      | 16              |        1          |   16        | 1     |
| adroit (v100)         |    10.4   | 166.0         |   1      | 1               |        1          |   1         | 2     |
| tigerGpu              |    11.1   | 156.1         |   1      | 1               |        1          |   1         | 1     |
| tigerGpu              |    23.0   | 75.1          |   2      | 1               |        1          |   2         | 1     |
| tigerGpu              |     8.0   | 216.6         |   1      | 2               |        1          |   2         | 1     |
| tigerGpu              |     6.0   | 288.8         |   1      | 4               |        1          |   4         | 1     |
| tigerGpu              |     5.2   | 333.9         |   1      | 8               |        1          |   8         | 1     |
| tigerGpu              |     5.3   | 325.1         |   1      |16               |        1          |  16         | 1     |
| tigerGpu              |    12.4   | 139.2         |   1      | 1               |        1          |   1         | 2     |
| tigerGpu              |     5.2   | 331.8         |   1      | 16              |        1          |   1         | 2     |
| tigerGpu              |     5.1   | 338.5         |   1      | 28              |        1          |   28        | 4     |
| traverse              |    16.9   | 102.0         |   1      | 1               |        1          |   1 (4)     | 1     |
| traverse              |    58.3   | 29.7          |   2      | 1               |        1          |   2 (8)     | 1     |
| traverse              |    18.5   | 93.2          |   1      | 2               |        1          |   2 (8)     | 1     |
| traverse              |    33.3   | 51.9          |   4      | 1               |        4          |   1 (4)     | 1     |
| traverse              |    86.5   | 20.0          |   4      | 1               |        1          |   4 (16)    | 1     |
| traverse              |     7.0   | 245.3         |   1      | 16              |        1          |   16 (64)   | 1     |
| traverse*             |     4.2   | 413.0         |   1      | 16              |        1          |   16 (64)   | 1     |
| traverse*             |     4.9   | 351.1         |   1      | 32              |        1          |   16 (64)   | 1     |
| traverse*             |    10.4   | 166.1         |   4      | 4               |        1          |   16 (64)   | 1     |
| traverse              |    10.6   | 162.9         |   1      | 16              |        4          |   4 (16)    | 1     |
| traverse              |    7.1    | 243.1         |   1      | 32              |        1          |   32 (128)  | 1     |
| traverse              |    11.4   | 151.1         |   1      | 32              |        4          |   8 (32)    | 1     |
| traverse              |    16.0   | 107.9         |   1      | 64              |        2          |   32 (128)  | 1     |
| traverse              |    19.1   | 90.5          |   1      | 64              |        4          |   16 (64)   | 1     |
| tigerCpu              |   116.0   | 14.9          |   1      | 1               |        1          |  1          | 0     |
| tigerCpu              |    59.9   | 28.8          |   2      | 1               |        1          |  2          | 0     |
| tigerCpu              |    34.0   | 50.9          |   4      | 1               |        1          |  4          | 0     |
| tigerCpu              |    27.5   | 62.7          |   8      | 1               |        1          |  8          | 0     |
| tigerCpu (fft)        |    27x5   | 62x7          |   8      | 1               |        1          |  8          | 0     |
| tigerCpu (gcc)        |    27x5   | 62x7          |   8      | 1               |        1          |  8          | 0     |
| tigerCpu              |    15.7   | 109.9         |   16     | 1               |        1          |  16         | 0     |
| tigerCpu              |     9.6   | 179.8         |   32     | 1               |        1          |  32         | 0     |
| tigerCpu (n)          |     9.6   | 179.5         |   32     | 1               |        1          |  32         | 0     |
| tigerCpu (2x)         |    19.0   | 181.8         |   32     | 1               |        1          |  32         | 0     |
| tigerCpu              |    30.1   |  57.4         |   2      | 4               |        1          |  8          | 0     |
| tigerCpu              |    28.7   |  60.2         |   4      | 2               |        1          |  8          | 0     |
| tigerCpu              |    26.3   |  65.8         |   1      | 8               |        1          |  8          | 0     |
| tigerCpu              |    26.3   |  65.8         |   1      | 8               |        1          |  8          | 0     |
| tigerCpu              |    14.7   | 117.9         |   1      | 16              |        1          |  16         | 0     |
| tigerCpu              |    10.4   | 166.6         |   1      | 32              |        1          |  32         | 0     |
| tigerCpu              |    14.9   | 116.2         |   4      | 8               |        1          |  32         | 0     |
| tigerCpu              |    15.4   | 112.0         |   8      | 4               |        1          |  32         | 0     |
| tigerCpu (n)          |    14.9   | 116.4         |   8      | 4               |        1          |  32         | 0     |
| perseus               |   186.8   |  9.3          |   1      | 1               |        1          |  1          | 0     |
| perseus               |   119.6   | 14.4          |   2      | 1               |        1          |  2          | 0     |
| perseus               |    63.3   | 27.3          |   4      | 1               |        1          |  4          | 0     |
| perseus               |    31.5   | 54.8          |   8      | 1               |        1          |  8          | 0     |
| perseus               |    32.3   | 53.5          |   4      | 2               |        1          |  8          | 0     |
| perseus               |    33.4   | 51.8          |   2      | 4               |        1          |  8          | 0     |
| perseus               |    30.6   | 56.5          |   1      | 8               |        1          |  8          | 0     |
| perseus               |    30.5   | 56.6          |   1      | 8               |        1          |  8          | 0     |
| perseus [2x]          |    61.2   | 56.5          |   1      | 8               |        1          |  8          | 0     |
| perseus               |    18.4   | 93.7          |   16     | 1               |        1          |  16         | 0     |
| perseus               |    21.1   | 81.9          |   4      | 4               |        1          |  16         | 0     |
| perseus (pme)         |    18.3   | 93.7          |   4      | 4               |        1          |  16         | 0     |
| della [2]             |   222.2   | 7.8           |   1      | 1               |        1          |  1         | 0     |
| della [3]             |   141.8   | 12.2          |   1      | 1               |        1          |  1         | 0     |
| della [3]             |    29.7   | 58.1          |   1      | 8               |        1          |  1         | 0     |
| della [3]             |    18.7   | 92.5          |   1      | 16              |        1          |  1         | 0     |
| della [3]             |   xxx     | xxx           |   16     | 1               |        1          |  1         | 0     |
| della [3]             |   9.2     | 187.8         |   8      | 2               |        (3 nodes)   |  48        | 0     |
| della [3]             |   32.6    | 53.1          |   2      | 8               |        (3 nodes)   |  48        | 0     |
| della [3]             |   32.6    | 251.7         |   16     | 1               |        (3 nodes)   |  48        | 0     |

* -pin on
[2] haswell node (avx2)
[3] cascade node (avx512)
(pme) `gmx mdrun -npme 1 -ntomp_pme 4 -ntmpi 4 -ntomp $SLURM_CPUS_PER_TASK -s bench.tpr`
(n) 16 ntasks-per-node
[fft] Replaced `-DGMX_FFT_LIBRARY=mkl` with `-DGMX_BUILD_OWN_FFTW=ON`

Make sure you have a gmx and mdrun_mpi for tigerCpu and one set for tigerGpu.

Be carefaul with memory requests on all jobs including multi-node jobs.

Below is the Slurm script for 1 core and 1 GPU on TigerGPU:

```bash
#!/bin/bash
#SBATCH --job-name=rnase         # create a short name for your job
#SBATCH --nodes=1                # node count
#SBATCH --ntasks=1               # total number of tasks across all nodes
#SBATCH --cpus-per-task=1        # cpu-cores per task (>1 if multi-threaded tasks)
#SBATCH --mem=4G                 # memory per node (4G is default)
#SBATCH --gres=gpu:1             # number of gpus per node
#SBATCH --time=00:10:00          # total run time limit (HH:MM:SS)

export OMP_NUM_THREADS=$SLURM_CPUS_PER_TASK
export GMX_MAXBACKUP=-1

module purge
module load intel/19.0/64/19.0.1.144
module load intel-mpi/intel/2019.1/64
module load cudatoolkit/10.2

BCH=../gpu_bench/rnase_cubic
gmx grompp -f $BCH/pme_verlet.mdp -c $BCH/conf.gro -p $BCH/topol.top -o bench.tpr
gmx mdrun -ntomp $SLURM_CPUS_PER_TASK -s bench.tpr
```

The contents of `md.log` is shown below for 1 core and 1 GPU on TigerGPU:

```
$ cat md.log
                      :-) GROMACS - mdrun_mpi, 2019.4 (-:

                            GROMACS is written by:
     Emile Apol      Rossen Apostolov      Paul Bauer     Herman J.C. Berendsen
    Par Bjelkmar      Christian Blau   Viacheslav Bolnykh     Kevin Boyd    
 Aldert van Buuren   Rudi van Drunen     Anton Feenstra       Alan Gray     
  Gerrit Groenhof     Anca Hamuraru    Vincent Hindriksen  M. Eric Irrgang  
  Aleksei Iupinov   Christoph Junghans     Joe Jordan     Dimitrios Karkoulis
    Peter Kasson        Jiri Kraus      Carsten Kutzner      Per Larsson    
  Justin A. Lemkul    Viveca Lindahl    Magnus Lundborg     Erik Marklund   
    Pascal Merz     Pieter Meulenhoff    Teemu Murtola       Szilard Pall   
    Sander Pronk      Roland Schulz      Michael Shirts    Alexey Shvetsov  
   Alfons Sijbers     Peter Tieleman      Jon Vincent      Teemu Virolainen 
 Christian Wennberg    Maarten Wolf   
                           and the project leaders:
        Mark Abraham, Berk Hess, Erik Lindahl, and David van der Spoel

Copyright (c) 1991-2000, University of Groningen, The Netherlands.
Copyright (c) 2001-2018, The GROMACS development team at
Uppsala University, Stockholm University and
the Royal Institute of Technology, Sweden.
check out http://www.gromacs.org for more information.

GROMACS is free software; you can redistribute it and/or modify it
under the terms of the GNU Lesser General Public License
as published by the Free Software Foundation; either version 2.1
of the License, or (at your option) any later version.

GROMACS:      mdrun_mpi, version 2019.4
Executable:   /home/jdh4/.local/bin/mdrun_mpi
Data prefix:  /home/jdh4/.local
Working dir:  /home/jdh4/gromacs/run1_rnase
Process ID:   7585
Command line:
  mdrun_mpi -s bench.tpr -c conf.gro

GROMACS version:    2019.4
Precision:          single
Memory model:       64 bit
MPI library:        MPI
OpenMP support:     enabled (GMX_OPENMP_MAX_THREADS = 64)
GPU support:        CUDA
SIMD instructions:  AVX2_256
FFT library:        Intel MKL
RDTSCP usage:       enabled
TNG support:        enabled
Hwloc support:      hwloc-1.11.8
Tracing support:    disabled
C compiler:         /opt/intel/compilers_and_libraries_2019.1.144/linux/bin/intel64/icc Intel 19.0.0.20181018
C compiler flags:    -march=core-avx2   -mkl=sequential  -std=gnu99  -Ofast -xCORE-AVX2 -mtune=broadwell -DNDEBUG -ip -funroll-all-loops -alias-const -ansi-alias -no-prec-div -fimf-domain-exclusion=14 -qoverride-limits  
C++ compiler:       /opt/intel/compilers_and_libraries_2019.1.144/linux/bin/intel64/icpc Intel 19.0.0.20181018
C++ compiler flags:  -march=core-avx2   -mkl=sequential  -std=c++11   -Ofast -xCORE-AVX2 -mtune=broadwell -DNDEBUG -ip -funroll-all-loops -alias-const -ansi-alias -no-prec-div -fimf-domain-exclusion=14 -qoverride-limits  
CUDA compiler:      /usr/local/cuda-10.1/bin/nvcc nvcc: NVIDIA (R) Cuda compiler driver;Copyright (c) 2005-2019 NVIDIA Corporation;Built on Wed_Apr_24_19:10:27_PDT_2019;Cuda compilation tools, release 10.1, V10.1.168
CUDA compiler flags:-gencode;arch=compute_60,code=sm_60;-use_fast_math;;; ;-march=core-avx2;-mkl=sequential;-std=c++11;-Ofast;-xCORE-AVX2;-mtune=broadwell;-DNDEBUG;-ip;-funroll-all-loops;-alias-const;-ansi-alias;-no-prec-div;-fimf-domain-exclusion=14;-qoverride-limits;
CUDA driver:        10.20
CUDA runtime:       10.10

Note: 28 CPUs configured, but only 1 were detected to be online.

Running on 1 node with total 1 cores, 1 logical cores, 1 compatible GPU
Hardware detected on host tiger-i22g13 (the node of MPI rank 0):
  CPU info:
    Vendor: Intel
    Brand:  Intel(R) Xeon(R) CPU E5-2680 v4 @ 2.40GHz
    Family: 6   Model: 79   Stepping: 1
    Features: aes apic avx avx2 clfsh cmov cx8 cx16 f16c fma hle htt intel lahf mmx msr nonstop_tsc pcid pclmuldq pdcm pdpe1gb popcnt pse rdrnd rdtscp rtm sse2 sse3 sse4.1 sse4.2 ssse3 tdt x2apic
  Hardware topology: Full, with devices
    Sockets, cores, and logical processors:
      Socket  0: [   0]
    Numa nodes:
      Node  0 (137338761216 bytes mem):   0
      Node  1 (137438953472 bytes mem):
      Latency:
               0     1
         0  1.00  2.10
         1  2.10  1.00
    Caches:
      L1: 32768 bytes, linesize 64 bytes, assoc. 8, shared 1 ways
      L2: 262144 bytes, linesize 64 bytes, assoc. 8, shared 1 ways
      L3: 36700160 bytes, linesize 64 bytes, assoc. 20, shared 1 ways
    PCI devices:
      0000:02:00.0  Id: 8086:24f0  Class: 0x0208  Numa: -1
      0000:03:00.0  Id: 10de:15f8  Class: 0x0302  Numa: -1
      0000:04:00.0  Id: 10de:15f8  Class: 0x0302  Numa: -1
      0000:00:11.4  Id: 8086:8d62  Class: 0x0106  Numa: -1
      0000:01:00.0  Id: 8086:1521  Class: 0x0200  Numa: -1
      0000:01:00.1  Id: 8086:1521  Class: 0x0200  Numa: -1
      0000:08:00.0  Id: 102b:0534  Class: 0x0300  Numa: -1
      0000:00:1f.2  Id: 8086:8d02  Class: 0x0106  Numa: -1
      0000:81:00.0  Id: 144d:a821  Class: 0x0108  Numa: -1
      0000:82:00.0  Id: 10de:15f8  Class: 0x0302  Numa: -1
      0000:83:00.0  Id: 10de:15f8  Class: 0x0302  Numa: -1
  GPU info:
    Number of GPUs detected: 1
    #0: NVIDIA Tesla P100-PCIE-16GB, compute cap.: 6.0, ECC: yes, stat: compatible


++++ PLEASE READ AND CITE THE FOLLOWING REFERENCE ++++
M. J. Abraham, T. Murtola, R. Schulz, S. Páll, J. C. Smith, B. Hess, E.
Lindahl
GROMACS: High performance molecular simulations through multi-level
parallelism from laptops to supercomputers
SoftwareX 1 (2015) pp. 19-25
-------- -------- --- Thank You --- -------- --------


++++ PLEASE READ AND CITE THE FOLLOWING REFERENCE ++++
S. Páll, M. J. Abraham, C. Kutzner, B. Hess, E. Lindahl
Tackling Exascale Software Challenges in Molecular Dynamics Simulations with
GROMACS
In S. Markidis & E. Laure (Eds.), Solving Software Challenges for Exascale 8759 (2015) pp. 3-27
-------- -------- --- Thank You --- -------- --------


++++ PLEASE READ AND CITE THE FOLLOWING REFERENCE ++++
S. Pronk, S. Páll, R. Schulz, P. Larsson, P. Bjelkmar, R. Apostolov, M. R.
Shirts, J. C. Smith, P. M. Kasson, D. van der Spoel, B. Hess, and E. Lindahl
GROMACS 4.5: a high-throughput and highly parallel open source molecular
simulation toolkit
Bioinformatics 29 (2013) pp. 845-54
-------- -------- --- Thank You --- -------- --------


++++ PLEASE READ AND CITE THE FOLLOWING REFERENCE ++++
B. Hess and C. Kutzner and D. van der Spoel and E. Lindahl
GROMACS 4: Algorithms for highly efficient, load-balanced, and scalable
molecular simulation
J. Chem. Theory Comput. 4 (2008) pp. 435-447
-------- -------- --- Thank You --- -------- --------


++++ PLEASE READ AND CITE THE FOLLOWING REFERENCE ++++
D. van der Spoel, E. Lindahl, B. Hess, G. Groenhof, A. E. Mark and H. J. C.
Berendsen
GROMACS: Fast, Flexible and Free
J. Comp. Chem. 26 (2005) pp. 1701-1719
-------- -------- --- Thank You --- -------- --------


++++ PLEASE READ AND CITE THE FOLLOWING REFERENCE ++++
E. Lindahl and B. Hess and D. van der Spoel
GROMACS 3.0: A package for molecular simulation and trajectory analysis
J. Mol. Mod. 7 (2001) pp. 306-317
-------- -------- --- Thank You --- -------- --------


++++ PLEASE READ AND CITE THE FOLLOWING REFERENCE ++++
H. J. C. Berendsen, D. van der Spoel and R. van Drunen
GROMACS: A message-passing parallel molecular dynamics implementation
Comp. Phys. Comm. 91 (1995) pp. 43-56
-------- -------- --- Thank You --- -------- --------


++++ PLEASE CITE THE DOI FOR THIS VERSION OF GROMACS ++++
https://doi.org/10.5281/zenodo.3460414
-------- -------- --- Thank You --- -------- --------


The number of OpenMP threads was set by environment variable OMP_NUM_THREADS to 1

Input Parameters:
   integrator                     = md
   tinit                          = 0
   dt                             = 0.002
   nsteps                         = 10000
   init-step                      = 0
   simulation-part                = 1
   comm-mode                      = Linear
   nstcomm                        = 100
   bd-fric                        = 0
   ld-seed                        = 1519669233
   emtol                          = 10
   emstep                         = 0.01
   niter                          = 20
   fcstep                         = 0
   nstcgsteep                     = 1000
   nbfgscorr                      = 10
   rtpi                           = 0.05
   nstxout                        = 0
   nstvout                        = 0
   nstfout                        = 0
   nstlog                         = 0
   nstcalcenergy                  = 100
   nstenergy                      = 500
   nstxout-compressed             = 0
   compressed-x-precision         = 1000
   cutoff-scheme                  = Verlet
   nstlist                        = 10
   ns-type                        = Grid
   pbc                            = xyz
   periodic-molecules             = false
   verlet-buffer-tolerance        = 0.005
   rlist                          = 0.9
   coulombtype                    = PME
   coulomb-modifier               = Potential-shift
   rcoulomb-switch                = 0
   rcoulomb                       = 0.9
   epsilon-r                      = 1
   epsilon-rf                     = inf
   vdw-type                       = Cut-off
   vdw-modifier                   = Potential-shift
   rvdw-switch                    = 0
   rvdw                           = 0.9
   DispCorr                       = No
   table-extension                = 1
   fourierspacing                 = 0.1125
   fourier-nx                     = 56
   fourier-ny                     = 56
   fourier-nz                     = 56
   pme-order                      = 4
   ewald-rtol                     = 1e-05
   ewald-rtol-lj                  = 0.001
   lj-pme-comb-rule               = Geometric
   ewald-geometry                 = 0
   epsilon-surface                = 0
   tcoupl                         = V-rescale
   nsttcouple                     = 10
   nh-chain-length                = 0
   print-nose-hoover-chain-variables = false
   pcoupl                         = No
   pcoupltype                     = Isotropic
   nstpcouple                     = -1
   tau-p                          = 1
   compressibility (3x3):
      compressibility[    0]={ 0.00000e+00,  0.00000e+00,  0.00000e+00}
      compressibility[    1]={ 0.00000e+00,  0.00000e+00,  0.00000e+00}
      compressibility[    2]={ 0.00000e+00,  0.00000e+00,  0.00000e+00}
   ref-p (3x3):
      ref-p[    0]={ 0.00000e+00,  0.00000e+00,  0.00000e+00}
      ref-p[    1]={ 0.00000e+00,  0.00000e+00,  0.00000e+00}
      ref-p[    2]={ 0.00000e+00,  0.00000e+00,  0.00000e+00}
   refcoord-scaling               = No
   posres-com (3):
      posres-com[0]= 0.00000e+00
      posres-com[1]= 0.00000e+00
      posres-com[2]= 0.00000e+00
   posres-comB (3):
      posres-comB[0]= 0.00000e+00
      posres-comB[1]= 0.00000e+00
      posres-comB[2]= 0.00000e+00
   QMMM                           = false
   QMconstraints                  = 0
   QMMMscheme                     = 0
   MMChargeScaleFactor            = 1
qm-opts:
   ngQM                           = 0
   constraint-algorithm           = Lincs
   continuation                   = false
   Shake-SOR                      = false
   shake-tol                      = 0.0001
   lincs-order                    = 4
   lincs-iter                     = 1
   lincs-warnangle                = 30
   nwall                          = 0
   wall-type                      = 9-3
   wall-r-linpot                  = -1
   wall-atomtype[0]               = -1
   wall-atomtype[1]               = -1
   wall-density[0]                = 0
   wall-density[1]                = 0
   wall-ewald-zfac                = 3
   pull                           = false
   awh                            = false
   rotation                       = false
   interactiveMD                  = false
   disre                          = No
   disre-weighting                = Conservative
   disre-mixed                    = false
   dr-fc                          = 1000
   dr-tau                         = 0
   nstdisreout                    = 100
   orire-fc                       = 0
   orire-tau                      = 0
   nstorireout                    = 100
   free-energy                    = no
   cos-acceleration               = 0
   deform (3x3):
      deform[    0]={ 0.00000e+00,  0.00000e+00,  0.00000e+00}
      deform[    1]={ 0.00000e+00,  0.00000e+00,  0.00000e+00}
      deform[    2]={ 0.00000e+00,  0.00000e+00,  0.00000e+00}
   simulated-tempering            = false
   swapcoords                     = no
   userint1                       = 0
   userint2                       = 0
   userint3                       = 0
   userint4                       = 0
   userreal1                      = 0
   userreal2                      = 0
   userreal3                      = 0
   userreal4                      = 0
   applied-forces:
     electric-field:
       x:
         E0                       = 0
         omega                    = 0
         t0                       = 0
         sigma                    = 0
       y:
         E0                       = 0
         omega                    = 0
         t0                       = 0
         sigma                    = 0
       z:
         E0                       = 0
         omega                    = 0
         t0                       = 0
         sigma                    = 0
grpopts:
   nrdf:       48056
   ref-t:         300
   tau-t:         0.1
annealing:          No
annealing-npoints:           0
   acc:	           0           0           0
   nfreeze:           N           N           N
   energygrp-flags[  0]: 0

Changing nstlist from 10 to 80, rlist from 0.9 to 1.038

Using 1 MPI process
Using 1 OpenMP thread 

1 GPU selected for this run.
Mapping of GPU IDs to the 2 GPU tasks in the 1 rank on this node:
  PP:0,PME:0
PP tasks will do (non-perturbed) short-ranged interactions on the GPU
PME tasks will do all aspects on the GPU
Pinning threads with an auto-selected logical core stride of 1
System total charge: 0.000
Will do PME sum in reciprocal space for electrostatic interactions.

++++ PLEASE READ AND CITE THE FOLLOWING REFERENCE ++++
U. Essmann, L. Perera, M. L. Berkowitz, T. Darden, H. Lee and L. G. Pedersen 
A smooth particle mesh Ewald method
J. Chem. Phys. 103 (1995) pp. 8577-8592
-------- -------- --- Thank You --- -------- --------

Using a Gaussian width (1/beta) of 0.288146 nm for Ewald
Potential shift: LJ r^-12: -3.541e+00 r^-6: -1.882e+00, Ewald -1.111e-05
Initialized non-bonded Ewald correction tables, spacing: 8.85e-04 size: 1018

Generated table with 1019 data points for 1-4 COUL.
Tabscale = 500 points/nm
Generated table with 1019 data points for 1-4 LJ6.
Tabscale = 500 points/nm
Generated table with 1019 data points for 1-4 LJ12.
Tabscale = 500 points/nm

Using GPU 8x8 nonbonded short-range kernels

Using a dual 8x4 pair-list setup updated with dynamic, rolling pruning:
  outer list: updated every 80 steps, buffer 0.138 nm, rlist 1.038 nm
  inner list: updated every 10 steps, buffer 0.002 nm, rlist 0.902 nm
At tolerance 0.005 kJ/mol/ps per atom, equivalent classical 1x1 list would be:
  outer list: updated every 80 steps, buffer 0.277 nm, rlist 1.177 nm
  inner list: updated every 10 steps, buffer 0.042 nm, rlist 0.942 nm

Using Lorentz-Berthelot Lennard-Jones combination rule

Removing pbc first time

Initializing LINear Constraint Solver

++++ PLEASE READ AND CITE THE FOLLOWING REFERENCE ++++
B. Hess and H. Bekker and H. J. C. Berendsen and J. G. E. M. Fraaije
LINCS: A Linear Constraint Solver for molecular simulations
J. Comp. Chem. 18 (1997) pp. 1463-1472
-------- -------- --- Thank You --- -------- --------

The number of constraints is 2053

++++ PLEASE READ AND CITE THE FOLLOWING REFERENCE ++++
S. Miyamoto and P. A. Kollman
SETTLE: An Analytical Version of the SHAKE and RATTLE Algorithms for Rigid
Water Models
J. Comp. Chem. 13 (1992) pp. 952-962
-------- -------- --- Thank You --- -------- --------

Center of mass motion removal mode is Linear
We have the following groups for center of mass motion removal:
  0:  rest

++++ PLEASE READ AND CITE THE FOLLOWING REFERENCE ++++
G. Bussi, D. Donadio and M. Parrinello
Canonical sampling through velocity rescaling
J. Chem. Phys. 126 (2007) pp. 014101
-------- -------- --- Thank You --- -------- --------

There are: 24040 Atoms

Constraining the starting coordinates (step 0)

Constraining the coordinates at t0-dt (step 0)
RMS relative constraint deviation after constraining: 1.20e-05
Initial temperature: 297.799 K

Started mdrun on rank 0 Sun Dec  8 12:18:29 2019

           Step           Time
              0        0.00000

   Energies (kJ/mol)
          Angle    Proper Dih.  Improper Dih.          LJ-14     Coulomb-14
    4.44104e+03    5.70374e+03    2.50388e+02    2.00471e+03    1.68037e+04
        LJ (SR)   Coulomb (SR)   Coul. recip.      Potential    Kinetic En.
    4.16574e+04   -3.84144e+05    3.38826e+03   -3.09894e+05    5.99546e+04
   Total Energy  Conserved En.    Temperature Pressure (bar)   Constr. rmsd
   -2.49940e+05   -2.49940e+05    3.00103e+02   -3.53243e+02    2.74440e-05

step  160: timed with pme grid 56 56 56, coulomb cutoff 0.900: 207.5 M-cycles
step  320: timed with pme grid 48 48 48, coulomb cutoff 1.046: 218.0 M-cycles
step  480: timed with pme grid 44 44 44, coulomb cutoff 1.141: 224.8 M-cycles
step  640: timed with pme grid 40 40 40, coulomb cutoff 1.255: 242.0 M-cycles
step  800: timed with pme grid 42 42 42, coulomb cutoff 1.196: 226.6 M-cycles
step  960: timed with pme grid 44 44 44, coulomb cutoff 1.141: 225.2 M-cycles
step 1120: timed with pme grid 48 48 48, coulomb cutoff 1.046: 212.7 M-cycles
step 1280: timed with pme grid 52 52 52, coulomb cutoff 0.966: 208.0 M-cycles
step 1440: timed with pme grid 56 56 56, coulomb cutoff 0.900: 205.1 M-cycles
step 1600: timed with pme grid 42 42 42, coulomb cutoff 1.196: 227.4 M-cycles
step 1760: timed with pme grid 44 44 44, coulomb cutoff 1.141: 224.7 M-cycles
step 1920: timed with pme grid 48 48 48, coulomb cutoff 1.046: 211.3 M-cycles
step 2080: timed with pme grid 52 52 52, coulomb cutoff 0.966: 208.3 M-cycles
step 2240: timed with pme grid 56 56 56, coulomb cutoff 0.900: 203.8 M-cycles
              optimal pme grid 56 56 56, coulomb cutoff 0.900
           Step           Time
          10000       20.00000

Writing checkpoint, step 10000 at Sun Dec  8 12:18:40 2019


   Energies (kJ/mol)
          Angle    Proper Dih.  Improper Dih.          LJ-14     Coulomb-14
    4.41865e+03    5.58752e+03    2.37679e+02    2.10368e+03    1.69947e+04
        LJ (SR)   Coulomb (SR)   Coul. recip.      Potential    Kinetic En.
    4.15408e+04   -3.84965e+05    3.40217e+03   -3.10680e+05    5.98743e+04
   Total Energy  Conserved En.    Temperature Pressure (bar)   Constr. rmsd
   -2.50806e+05   -2.50415e+05    2.99701e+02   -3.61317e+02    2.64731e-05

	<======  ###############  ==>
	<====  A V E R A G E S  ====>
	<==  ###############  ======>

	Statistics over 10001 steps using 101 frames

   Energies (kJ/mol)
          Angle    Proper Dih.  Improper Dih.          LJ-14     Coulomb-14
    9.36333e+02    1.16192e+03    5.47287e+01    4.29936e+02    3.48874e+03
        LJ (SR)   Coulomb (SR)   Coul. recip.      Potential    Kinetic En.
    8.76572e+03   -8.00033e+04    6.59049e+02   -6.45069e+04    1.24766e+04
   Total Energy  Conserved En.    Temperature Pressure (bar)   Constr. rmsd
   -5.20303e+04   -5.20105e+04    2.99933e+02   -2.49091e+02    0.00000e+00

   Total Virial (kJ/mol)
    4.47764e+03   -3.72688e+01   -1.63568e+01
   -3.74303e+01    4.56523e+03   -6.36261e+01
   -1.67602e+01   -6.38424e+01    2.17725e+04

   Pressure (bar)
   -4.20373e+01    6.80350e+00    5.26328e-01
    6.82519e+00   -5.49220e+01    9.37403e+00
    5.80520e-01    9.40308e+00   -2.39563e+02


	M E G A - F L O P S   A C C O U N T I N G

 NB=Group-cutoff nonbonded kernels    NxN=N-by-N cluster Verlet kernels
 RF=Reaction-Field  VdW=Van der Waals  QSTab=quadratic-spline table
 W3=SPC/TIP3p  W4=TIP4p (single or pairs)
 V&F=Potential and force  V=Potential only  F=Force only

 Computing:                               M-Number         M-Flops  % Flops
-----------------------------------------------------------------------------
 Pair Search distance check             271.172368        2440.551     0.0
 NxN Ewald Elec. + LJ [F]            190236.746752    12555625.286    97.9
 NxN Ewald Elec. + LJ [V&F]            1936.924480      207250.919     1.6
 1,4 nonbonded interactions              53.415341        4807.381     0.0
 Shift-X                                  3.029040          18.174     0.0
 Angles                                  37.043704        6223.342     0.0
 Propers                                 55.825582       12784.058     0.1
 Impropers                                4.220422         877.848     0.0
 Virial                                   2.432585          43.787     0.0
 Stop-CM                                  2.452080          24.521     0.0
 Calc-Ekin                               48.128080        1299.458     0.0
 Lincs                                   20.536159        1232.170     0.0
 Lincs-Mat                              444.613344        1778.453     0.0
 Constraint-V                           261.192228        2089.538     0.0
 Constraint-Vir                           2.430161          58.324     0.0
 Settle                                  73.382008       23702.389     0.2
-----------------------------------------------------------------------------
 Total                                                12820256.198   100.0
-----------------------------------------------------------------------------


     R E A L   C Y C L E   A N D   T I M E   A C C O U N T I N G

On 1 MPI rank

 Computing:          Num   Num      Call    Wall time         Giga-Cycles
                     Ranks Threads  Count      (s)         total sum    %
-----------------------------------------------------------------------------
 Neighbor search        1    1        126       1.165          2.796  10.7
 Launch GPU ops.        1    1      20002       0.785          1.884   7.2
 Force                  1    1      10001       3.011          7.225  27.6
 Wait PME GPU gather    1    1      10001       0.068          0.163   0.6
 Reduce GPU PME F       1    1      10001       0.349          0.837   3.2
 Wait GPU NB local      1    1      10001       0.113          0.272   1.0
 NB X/F buffer ops.     1    1      19876       1.161          2.785  10.7
 Write traj.            1    1          1       0.051          0.123   0.5
 Update                 1    1      10001       0.738          1.771   6.8
 Constraints            1    1      10003       3.137          7.528  28.8
 Rest                                           0.312          0.749   2.9
-----------------------------------------------------------------------------
 Total                                         10.889         26.133 100.0
-----------------------------------------------------------------------------

NOTE: 11 % of the run time was spent in pair search,
      you might want to increase nstlist (this has no effect on accuracy)

               Core t (s)   Wall t (s)        (%)
       Time:       10.889       10.889      100.0
                 (ns/day)    (hour/ns)
Performance:      158.709        0.151
Finished mdrun on rank 0 Sun Dec  8 12:18:40 2019
```
