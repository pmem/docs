# Introduction

## Persistent Memory Overview

Over the last few decades, computer systems have implemented the memory-storage hierarchy shown in Figure 1. The memory-storage hierarchy utilizes the principle of locality which keeps frequently accessed data closest to the CPU. Successive generations of technologies have iterated on the number, size, and speed of caches to ensure the CPUs have access to the most frequently used data. CPU speeds have continued to get faster, adding more cores and threads with each new CPU generations as they try to maintain [Moore's Law](https://en.wikipedia.org/wiki/Moore's_law). The capacity, price and speed of volatile DRAM and non-volatile storage such as NAND SSDs or Hard Disk Drives have not kept pace and quickly become a bottleneck for system and application performance.

![Figure 1: Memory Storage Hierarchy](../.gitbook/assets/memory-storage-hierachy-default.png)

Persistent Memory \(PMEM\), also referred to as Non-Volatile Memory \(NVM\) or Storage Class Memory \(SCM\), provides a new entry in the memory-storage hierarchy shown in Figure 2 that fills the performance/capacity gap.

![Figure 2: Memory-Storage Hierarchy with Persistent Memory Tier](../.gitbook/assets/memory-storage-hierachy-persistent-memory%20%282%29.png)

With persistent memory, applications have a new tier available for data placement. In addition to the memory and storage tiers, the persistent memory tier offers greater capacity than DRAM and significantly faster performance than storage. Applications can access persistent memory like they do with traditional memory, eliminating the need to page blocks of data back and forth between memory and storage.

## SNIA Programming Model

The [Storage Network Industry Association \(SNIA\)](http://www.snia.org) and several technology industry companies derived several standards including the "Non-Volatile Programming Model \(NVM\)" to leverage development using persistent memory. [NVM Programming Model Version 1.2](https://www.snia.org/sites/default/files/technical_work/final/NVMProgrammingModel_v1.2.pdf) can be found on the [SNIA Persistent Memory website](https://www.snia.org/PM). Each year SNIA hosts a Non-Volatile conference. Papers and recordings of previous conferences can be found on the [SNIA Persistent Memory home page](https://www.snia.org/PM).

## What does this mean for application developers?

The introduction of a persistent memory tier offers application developers a choice of where to put data and data structures. Traditionally data was read and written to volatile memory and flushed to non-volatile persistent storage. When the application is started, data has to be read from storage into volatile memory before it can be accessed. Depending on the size of the working dataset, this can take seconds, minutes, or hours. With clever application design, developers and application architects can now take advantage of this new technology to improve performance and reduce application startup times.

Persistent Memory introduces a new programming paradigm and issues not faced before by developers. These include, but not limited to:

* Data Persistence
  * Stores are not guaranteed to be persistent until flushed.  This requires flushing all CPU caches to the 'persistence domain'.  The Persistence Domain is the area of the system that, in the event of a power failure, will guarantee the data to be automatically flushed to persistent storage.
  * CPUs have out-of-order CPU execution and cache access/flushing.  This means if two values are stored by the application, the order in which they arrive at the persistent memory may not be the order that the application wrote them.  
* Data Consistency:
  * Data written in block sizes below CPU cache line sizes, typically 64 bytes on modern x86/x64 CPUs, will be written entirely or not at all.  
  * Data written in block sizes above CPU cache lines sizes may encounter torn blocks.  This means some data may be written to persistent storage and some may not in the event of a server power outage.
* Memory Leaks:
  * Memory leaks to persistent storage are persistent.  Rebooting the server doesn't change the on-device contents.  In the current volatile model, if an application leaks memory, restarting the application or system frees that memory.
* Byte Level Access:
  * Application developers can read and write at the byte level according to the application requirements.  The read/writes no longer need to be aligned or equal to storage block boundaries, eg: 512byte, 4KiB, or 8KiB.  The storage doesn't need to read an entire block to modify a few bytes, to then write that entire block back to persistent storage.  Applications are free to read/write as much or as little as required.  This improves performance and reduces memory footprint overheads.
* Error Handling:
  * Applications need to detect and handle hardware errors directly.  Since applications have direct access to the persistent memory media, any errors will be returned back to the application as memory errors for it to handle.  

Application Developers can use traditional system calls such as memcpy, memmap, etc to access the persistent memory devices. This is a challenging route to take as it requires intimate knowledge of the persistent memory device and CPU features. Applications developed for one server platform may not be portable to a different platform or CPU generation. To help application developers with these challenges, an open source [Persistent Memory Developer Kit](http://pmem.io/pmdk/) can be downloaded and freely used. More information can be found in the '[PMDK Introduction](what-is-pmdk.md)', '[Installing PMDK](installing-pmdk/)', and '[PMDK Programmers Guide](introduction.md)' found within this document collection. Tuned and validated on both Linux and Windows, the libraries build on the Direct Access \(DAX\) feature of those operating systems which allows applications to access persistent memory as memory-mapped files, as described in the [SNIA NVM Programming Model](introduction.md#snia-programming-model).

Managing persistent memory devices on Linux is achieved using the `ndctl` utility. Read the '[NDCTL Introduction](what-is-ndctl.md)', '[Installing NDCTL](installing-ndctl.md)', then '[NDCTL Users Guide](../ndctl-users-guide/)' within this collection. Windows users can use the `Get-PhysicalDisk` power-shell command. More information can be found on the [Microsoft Documents](https://docs.microsoft.com/en-us/windows/desktop/persistent-memory-programming-in-windows---nvml-integration) site.

## Reading Collection

The following is a list of documents in this collection in the suggested reading order:

* [**Getting Started Guide**](./) \(this document\): Describes how to install and configure the Persistent Memory Developer Kit \(PMDK\) and Non-Volatile Device Control \(NDCTL\).  It is designed to get users up and running quickly with the software.
* [**Changelog**](../changelog/): Describes the historical log or record of all notable changes made to the projects.
* [**NDCTL Users Guide**](../ndctl-users-guide/)**:** Describes how to use the ndctl, daxctl, and daxio utilities to configure, manage, and monitor real or emulated Non-Volatile Devices.

