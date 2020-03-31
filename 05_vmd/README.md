# VMD on the HPC Clusters

For intensive visualization work you can install [VMD](https://www.ks.uiuc.edu/Research/vmd/) in your account on tigressdata. The `/scratch/gpfs` filesystems are
accessible from tigressdata. For example, for Perseus use the path: `/perseus/scratch/gpfs/<YourNetID>`.

For just doing quick checks of system configurations you can also install it in your `/home` directory on one of the
clusters (e.g., tiger).

It is recommended that you should install [TurboVNC](https://researchcomputing.princeton.edu/turbovnc) to work with VMD.

If you decide to rely on X11 forwarding then make sure you are aware of [this page](https://researchcomputing.princeton.edu/sshX).

I found the following procedure to work for installing VMD on tigressdata:

```
ssh -X tigressdata.princeton.edu
cd software  # or another directory
wget https://www.ks.uiuc.edu/Research/vmd/vmd-1.9.4/files/alpha/vmd-1.9.4a38.bin.LINUXAMD64-CUDA9-OptiX510-OSPRay170.opengl.tar.gz
tar zxvf vmd-1.9.4a38.bin.LINUXAMD64-CUDA9-OptiX510-OSPRay170.opengl.tar.gz
cd vmd-1.9.4a38
# read the README file for the quick install directions then
# modify the configure file with something like
$install_bin_dir="/home/jdh4/software/vmd/bin";
$install_library_dir="/home/jdh4/software/vmd/lib/$install_name";
cd src
make install
# launch VMD with something like the next line
$HOME/software/vmd/bin/vmd
# you coud add the above to your PATH in .bashrc
```
