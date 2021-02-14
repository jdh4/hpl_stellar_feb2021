# HPL on Stellar

```
$ ssh stellar
$ cd software
$ wget https://www.netlib.org/benchmark/hpl/hpl-2.3.tar.gz
$ tar zxf hpl-2.3.tar.gz
$ cd hpl-2.3
$ cp setup/Make.Linux_Intel64 .
$ vim Make.Linux_Intel64 (build1)
$ module load intel/2021.1.2 intel-mpi/intel/2021.1.1
$ make arch=Linux_Intel64
# make clean arch=Linux_Intel64
```

```
$ ldd xhpl 
	linux-vdso.so.1 (0x00007fff5b786000)
	libpthread.so.0 => /lib64/libpthread.so.0 (0x000014820645b000)
	libdl.so.2 => /lib64/libdl.so.2 (0x0000148206257000)
	libmpifort.so.12 => /opt/intel/oneapi/mpi/2021.1.1/lib/libmpifort.so.12 (0x0000148205e99000)
	libmpi.so.12 => /opt/intel/oneapi/mpi/2021.1.1/lib/release/libmpi.so.12 (0x0000148204b20000)
	librt.so.1 => /lib64/librt.so.1 (0x0000148204918000)
	libm.so.6 => /lib64/libm.so.6 (0x0000148204596000)
	libiomp5.so => /opt/intel/oneapi/compiler/2021.1.2/linux/compiler/lib/intel64_lin/libiomp5.so (0x000014820418f000)
	libgcc_s.so.1 => /lib64/libgcc_s.so.1 (0x0000148203f77000)
	libc.so.6 => /lib64/libc.so.6 (0x0000148203bb4000)
	/lib64/ld-linux-x86-64.so.2 (0x000014820667b000)
	libfabric.so.1 => /opt/intel/oneapi/mpi/2021.1.1/libfabric/lib/libfabric.so.1 (0x000014820396e000)
```

```
$ ldd xhpl 
	linux-vdso.so.1 (0x00007ffddea72000)
	libmkl_intel_lp64.so.1 => /opt/intel/oneapi/mkl/2021.1.1/lib/intel64/libmkl_intel_lp64.so.1 (0x0000150b0b005000)
	libmkl_intel_thread.so.1 => /opt/intel/oneapi/mkl/2021.1.1/lib/intel64/libmkl_intel_thread.so.1 (0x0000150b0770c000)
	libmkl_core.so.1 => /opt/intel/oneapi/mkl/2021.1.1/lib/intel64/libmkl_core.so.1 (0x0000150aff746000)
	libiomp5.so => /opt/intel/oneapi/compiler/2021.1.2/linux/compiler/lib/intel64_lin/libiomp5.so (0x0000150aff33f000)
	libpthread.so.0 => /lib64/libpthread.so.0 (0x0000150aff11f000)
	libm.so.6 => /lib64/libm.so.6 (0x0000150afed9d000)
	libdl.so.2 => /lib64/libdl.so.2 (0x0000150afeb99000)
	libmpifort.so.12 => /opt/intel/oneapi/mpi/2021.1.1/lib/libmpifort.so.12 (0x0000150afe7db000)
	libmpi.so.12 => /opt/intel/oneapi/mpi/2021.1.1/lib/release/libmpi.so.12 (0x0000150afd462000)
	librt.so.1 => /lib64/librt.so.1 (0x0000150afd25a000)
	libgcc_s.so.1 => /lib64/libgcc_s.so.1 (0x0000150afd042000)
	libc.so.6 => /lib64/libc.so.6 (0x0000150afcc7f000)
	/lib64/ld-linux-x86-64.so.2 (0x0000150b0bd40000)
	libfabric.so.1 => /opt/intel/oneapi/mpi/2021.1.1/libfabric/lib/libfabric.so.1 (0x0000150afca39000)
```

Baseline HPL.dat:

```
HPLinpack benchmark input file
Innovative Computing Laboratory, University of Tennessee
out.hpl      output file name (if any)
8            device out (6=stdout,7=stderr,file)
1            # of problems sizes (N)
100000       Ns
1            # of NBs
4            NBs
0            PMAP process mapping (0=Row-,1=Column-major)
1            # of process grids (P x Q)
12           Ps
16           Qs
16.0         threshold
3            # of panel fact
0 1 2        PFACTs (0=left, 1=Crout, 2=Right)
2            # of recursive stopping criterium
4 8          NBMINs (>= 1)
1            # of panels in recursion
2            NDIVs
3            # of recursive panel fact.
0 1 2        RFACTs (0=left, 1=Crout, 2=Right)
1            # of broadcast
1            BCASTs (0=1rg,1=1rM,2=2rg,3=2rM,4=Lng,5=LnM)
1            # of lookahead depth
1            DEPTHs (>=0)
2            SWAP (0=bin-exch,1=long,2=mix)
64           swapping threshold
0            L1 in (0=transposed,1=no-transposed) form
0            U  in (0=transposed,1=no-transposed) form
1            Equilibration (0=no,1=yes)
8            memory alignment in double (> 0)
```


```bash
#!/bin/bash
#SBATCH --job-name=hpl-192       # create a short name for your job
#SBATCH --nodes=2                # node count
#SBATCH --ntasks-per-node=96     # total number of tasks across all nodes
#SBATCH --cpus-per-task=1        # cpu-cores per task (>1 if multi-threaded tasks)
#SBATCH --mem=765000M
#SBATCH --time=16:00:00          # total run time limit (HH:MM:SS)

module purge
module load intel/2021.1.2
module load intel-mpi/intel/2021.1.1

export OMP_NUM_THREADS=$SLURM_CPUS_PER_TASK

CUR=$(pwd)
MYDIR=/home/jdh4/software/hpl-2.3/bin/Linux_Intel64

cd $MYDIR
srun ./xhpl > std.out
mv $MYDIR/std.out $MYDIR/out.HPL $CUR
```

Results
| executable | nodes | N      | NB | P  | Q   | Gflops  | runtime | memory (GB) |
| ---------- | ----- | --     | -- | -- | --- | ------- | ------  | ----------- |
| build1     |   2   | 10000  | 4  |12  | 16  | 718     | 28 s    | ------ | 
| build1     |   2   | 100000 | 4  |12  | 16  | 402     |         | ------ |
| build2     |   2   | 10000  | 4  |12  | 16  | 764     | 28 s    | ------ | 
| build2     |   4   | 10000  | 4  |24  | 16  | 1870    | 11 s    | 26.6   |
| build2     |   4   | 10000  |100 |24  | 16  | 1700    | 12 s    | 111.9   |
