# System Requirements

The following minimum system requirements are required to support either physical NVDIMMs or emulated NVDIMMs.

* Linux Kernel Version 4.0 or later
  * Kernel Version 4.15 or later is recommended for production due to performance improvements\*
* 4GB of DDR Memory \(Minimum\), 16GB or more recommended for Emulated NVDIMMs.

\(\*\) - The 4.15 Linux kernel introduced a new flag to mmap\(\) called MAP\_SYNC. If the mapping with MAP\_SYNC is successful, PMDK knows that flushing from user space using instructions like CLWB is safe. Otherwise, it falls back to calling msync\(\). The msync\(\) call can be slower for the PMDK-style transactions because they are fine-grained, flushing lots of little stores during a typical transaction. Calling into the kernel has an overhead, as does the code path that msync\(\) takes.

Setting PMEM\_IS\_PMEM\_FORCE=1 tells the PMDK libraries "even if mmap\(\) with MAP\_SYNC was unsuccessful, pretend it worked and do user space flushing anyway, avoiding calls to msync\(\)". This environment variable is meant for testing, not for production use. For production environments use the 4.15 or later kernel. Both FSDAX and DEVDAX use user space flushing when it is available.

* Windows Server 2016 or later
* 4GB of DDR Memory \(Minimum\), 16GB or more recommended for Emulated NVDIMMs.

