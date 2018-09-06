# PMDK Introduction

With persistent memory, applications have a new tier available for data placement as shown in Figure 1. In addition to the memory and storage tiers, the persistent memory tier offers greater capacity than DRAM and significantly faster performance than storage. Applications can access persistent memory resident data structures in-place, like they do with traditional memory, eliminating the need to page blocks of data back and forth between memory and storage.

![](../.gitbook/assets/pmem_storage_pyramid.jpg)

To get this low-latency direct access, a new software architecture is required that allows applications to access ranges of persistent memory.

The [Persistent Memory Development Kit \(PMDK\)](http://pmem.io/pmdk) is a collection of libraries and tools for System Administrators and Application Developers to simplify managing and accessing persistent memory devices. Tuned and validated on both Linux and Windows, the libraries build on the Direct Access \(DAX\) feature which allows applications to directly access persistent memory as memory-mapped files. This is described in detail in the [Storage Network Industry Association \(SNIA\) NVM Programming Model](https://www.snia.org/sites/default/files/technical_work/final/NVMProgrammingModel_v1.2.pdf). Figure 2 shows the model which describes how applications can access persistent memory devices \(NVDIMMs\) using traditional POSIX standard APIs such as read, write, pread, and pwrite, or load/store operations such as memcpy when the data is memory mapped to the application. The 'Persistent Memory' area describes the fastest possible access because the application I/O bypasses existing filesystem page caches and goes directly to/from the persistent memory media.

![Fig 2: SNIA Programming Model](https://github.com/pmem/docs/tree/f786ed7c6b0ae701f299a839c07ef9a35a7d3f4f/.gitbook/assets/snia_programming_model.png)

Directly accessing the physical media introduces new programming challenges and paradigms. The PMDK offers application developers many libraries and features highlighted below to solve some of the more difficult programming issues:

**Available Libraries:**

* [**libpmem**](http://pmem.io/pmdk/libpmem/)**:**  provides low-level persistent memory support
* [**libpmemobj**](http://pmem.io/pmdk/libpmemobj/)**:**  provides a transactional object store, providing memory allocation, transactions, and general facilities for persistent memory programming.
* [**libpmemblk**](http://pmem.io/pmdk/libpmemblk/)**:**  supports arrays of pmem-resident blocks, all the same size, that are atomically updated.
* [**libpmemlog**](http://pmem.io/pmdk/libpmemlog/)**:**  provides a pmem-resident log file.
* [**libpmemcto**](http://pmem.io/pmdk/libpmemcto/)**:**  provides common malloc-like interfaces to persistent memory pools built on memory-mapped files, with no overhead imposed by run-time flushing or transactional updates.
* [**libvmem**](http://pmem.io/pmdk/libvmem/)**:**  turns a pool of persistent memory into a volatile memory pool, similar to the system heap but kept separate and with its own malloc-style API.
* [**libvmmalloc**](http://pmem.io/pmdk/libvmmalloc/)**:**  library transparently converts all the dynamic memory allocations into persistent memory allocations.
* [**libpmempool**](http://pmem.io/pmdk/libpmempool/)**:**  provides support for off-line pool management and diagnostics.
* [**librmem**](http://pmem.io/pmdk/librpmem/)**:**  provides low-level support for remote access to _persistent memory_ utilizing RDMA-capable RNICs.

**Available Utilities**:

* [**pmempool**](http://pmem.io/pmdk/pmempool/)**:** Manage and analyze persistent memory pools with this stand-alone utility
* [**pmemcheck**](http://pmem.io/2015/07/17/pmemcheck-basic.html)**:** Use dynamic runtime analysis with an enhanced version of Valgrind for use with persistent memory.

**Supporting Documents**

* **Introduction to Programming with Persistent Memory from Intel:** [https://software.intel.com/en-us/articles/introduction-to-programming-with-persistent-memory-from-intel](https://software.intel.com/en-us/articles/introduction-to-programming-with-persistent-memory-from-intel)

**Supporting Videos**

* **Get Started with Persistent Memory Programming \(Series\):**

  The non-volatile memory library \(NVML\) is now called the Persistent Memory Developer Kit \(PMDK\). In these videos, its architect, Andy Rudoff, introduces you to persistent memory programming and shows you how to apply it to your application.

  * Part 1: [What is Persistent Memory?](https://software.intel.com/en-us/persistent-memory/get-started/series)
  * Part 2: [Describing The SNIA Programming Model](https://software.intel.com/en-us/videos/the-nvm-programming-model-persistent-memory-programming-series)
  * Part 3: [Introduction to PMDK Libraries](https://software.intel.com/en-us/videos/intro-to-the-nvm-libraries-persistent-memory-programming-series)
  * Part 4: [Thinking Transactionally](https://software.intel.com/en-us/videos/thinking-transactionally-persistent-memory-programming-series)
  * Part 5: [A C++ Example](https://software.intel.com/en-us/videos/a-c-example-persistent-memory-programming-series)

