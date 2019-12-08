# Benchmarks

GPU benchmark:

```
wget ftp://ftp.gromacs.org/pub/benchmarks/rnase_bench_systems.tar.gz
```

The CPU benchmark input files were obtained from [this page](http://www.gromacs.org/About_Gromacs/Benchmarks):

```
$ wget ftp://ftp.gromacs.org/pub/benchmarks/gmxbench-3.0.tar.gz
```

## RNASE-cubic (single node)

| cluster               | wall time (s)  | ns/day   |  ntasks  |  cpus-per-task  |  threads-per-core | total cores |  GPU  |
|:----------------------|----------:|:-------------:|---------:|:---------------:|:-----------------:|------------:|:-----:|
| perseus               |   228.9   |  7.5          |   1      | 1               |        1          |  1          | 0     |
| perseus               |   109.2   | 15.8          |   2      | 1               |        1          |  2          | 0     |
| perseus               |    60.0   | 28.8          |   4      | 1               |        1          |  4          | 0     |
| perseus               |    32.2   | 53.6          |   8      | 1               |        1          |  8          | 0     |
| perseus               |    32.9   | 52.5          |   4      | 2               |        1          |  8          | 0     |
| perseus               |    17.6   | 98.2          |   16     | 1               |        1          |  16         | 0     |
| perseus               |    92.1   | 18.8          |   4      | 4               |        1          |  16         | 0     |
| traverse              |    16.9   | 102.0         |   1      | 1               |        1          |   1         | 1     |
| traverse              |    16.9   | 102.5         |   1      | 1               |        4          |   1         | 1     |
| traverse              |    58.3   | 29.7          |   2      | 1               |        1          |   2         | 1     |
| traverse              |    18.5   | 93.2          |   1      | 2               |        1          |   2         | 1     |
| traverse              |    xxxx   | xxxx          |   1      | 1               |        1          |   1         | 2     |
