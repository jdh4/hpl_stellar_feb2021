# HPL on Stellar

```
$ ssh stellar
$ cd software
$ wget https://www.netlib.org/benchmark/hpl/hpl-2.3.tar.gz
$ tar zxf hpl-2.3.tar.gz
$ cd hpl-2.3
$ cp setup/Make.Linux_Intel64 .
$ vim Make.Linux_Intel64 (build1)
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
