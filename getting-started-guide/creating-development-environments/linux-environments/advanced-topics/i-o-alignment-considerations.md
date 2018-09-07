# I/O Alignment Considerations

Traditional storage devices such as Hard Disk Drives, SSD’s, NVMe, M.2, and SAN LUNs present storage as blocks. A block is an addressable unit of storage measured in bytes. The traditional block size used by hard disks is 512 bytes. Newer devices commonly use 4KiB or 8KiB physical block sizes, but may also choose to present logical/emulated 512 bytes blocks.

Persistent Memory devices are accessible via the Virtual Memory System. Therefore, IO should be aligned using the systems Page Size\(s\). The Memory Management Unit \(MMU\) located on the CPU determines what page sizes are possible.

Linux supports several page sizes:

* Default Page Size; is commonly 4KiB by default on all architectures. Linux often refers to these as a Page Table Entry \(PTE\).
* [Huge Pages](https://www.kernel.org/doc/Documentation/vm/hugetlbpage.txt); requires Kernel support having configured `CONFIG_HUBETLB_PAGE` and `CONFIG_HUGETLBFS`. Often referred to as the Page Middle Directory \(PMD\), huge pages are commonly 2MiB in size although modern kernel's also support 1GiB page sizes.

More information can be found in [Chapter 3 Page Table Management](https://www.kernel.org/doc/gorman/html/understand/understand006.html) of Mel Gorman’s book [Understanding the Linux Virtual Memory Manager](https://www.kernel.org/doc/gorman/html/understand/).

The page size is a compromise between memory usage and speed.

* A larger page size means more waste when a page is partially used.
* A smaller page size with a large memory capacity means more kernel memory for the page tables since there’s a large number of page table entries.
* A smaller page size could require more time spent in page table traversal, particularly if there’s a high Translation Lookaside Buffer \(TLB\) miss count.

The capacity difference between DDR and Persistent Memory Modules is considerable. Using smaller pages on a system with terabytes of memory could negatively impact performance for the reasons described above.

## Verifying Page Size Support

The systems default page size can be found by querying its configuration using the `getconf`command:

```text
$ getconf PAGE_SIZE
4096
```

or

```text
$ getconf PAGESIZE
4096
```

**NOTE:** The above units are bytes. 4096 bytes == 4 Kilobytes \(4 KiB\)

To verify the system currently has HugePage support, `cat /proc/meminfo|grep -i hugepage`will return information similar to the following:

```text
.....
HugePages_Total: uuu
HugePages_Free:  vvv
HugePages_Rsvd:  www
HugePages_Surp:  xxx
Hugepagesize:    yyy kB
Hugetlb:         zzz kB
.....
```

Depending on the processor, there are at least two different huge page sizes on the x86\_64 architecture: 2MiB and 1GiB. If the CPU supports 2MiB pages, it has the `PSE` cpuinfo flag, for 1GiB it has the `PDPE1GB` flag. `/proc/cpuinfo` shows whether the two flags are set.

If this commands returns a non-empty string, 2MiB pages are supported.

```text
$ grep pse /proc/cpuinfo | uniq
flags           : [...] pse [...]
```

If this commands returns a non-empty string, 1GiB pages are supported.

```text
$ grep pdpe1gb /proc/cpuinfo | uniq
flags           : [...] pdpe1gb [...]
```

## **Verifying IO Alignment**

For a DAX filesystem to be able to use 2 MiB hugepages several things have to happen:

* The mmap\(\) mapping has to be at least 2 MiB in size.
* The filesystem block allocation has to be at least 2 MiB in size.
* The filesystem block allocation has to have the same alignment as our mmap\(\).

The first requirement is trivial to control since the size of the mapping relates to the size of the persistent memory pool file\(s\). Both EXT4 and XFS each have support for requesting specific filesystem block allocation alignment and size. This feature was introduced in support of RAID, but can be used equally well for DAX filesystems. Finally, controlling the starting alignment is achieved by ensuring the start of the filesystem is 2MB aligned.

The procedure to ensure DAX filesystems use PMDs is shown below as an example. It needs to be executed once the dm-linear or dm-stripe has been configured.

1\) Verify the namespace is in fsdax mode.

```text
$ ndctl list -u
[
  {
    "dev":"namespace1.0",
    "mode":"fsdax",
    "size":"3.93 GiB (4.22 GB)",
    "uuid":"9b8d6eeb-547c-4865-8213-746c6b20bc9c",
    "raw_uuid":"e84e23f4-cab8-4afe-9fbf-6176e37095b1",
    "sector_size":512,
    "blockdev":"pmem1",
    "numa_node":0
  },
  {
    "dev":"namespace0.0",
    "mode":"fsdax",
    "size":"3.93 GiB (4.22 GB)",
    "uuid":"76a114c5-b7c0-48f7-8fe8-d09702d8b1b1",
    "raw_uuid":"2ef21ac8-e22d-4b8e-8cf6-fc0fcbf6258a",
    "sector_size":512,
    "blockdev":"pmem0",
    "numa_node":0
  }
]
```

If the namespace is not in fsdax mode, use the following to switch modes.

```text
$ sudo ndctl create-namespace -f -e namespace0.0 --mode=fsdax
$ sudo ndctl create-namespace -f -e namespace1.0 --mode=fsdax
```

**Note**: This will destroy all data within the namespace so backup any existing data before switching modes.

2\) Verify the persistent memory block device starts at a 2 MiB aligned physical address.

This is important because when we ask the filesystem for 2 MiB aligned and sized block allocations it will provide those block allocations relative to the beginning of its block device. If the filesystem is built on top of a namespace whose data starts at a 1 MiB aligned offset, for example, a block allocation that is 2 MiB aligned from the point of view of the filesystem will still be only 1 MiB aligned from DAX’s point of view. This will cause DAX to fall back to 4 KiB page faults.

Use `/proc/iomem` to verify the starting address of the namespace, eg:

```text
$ cat /proc/iomem
...
140000000-23fdfffff : Persistent Memory
  140000000-23fdfffff : namespace0.0
23fe00000-33fbfffff : Persistent Memory
  23fe00000-33fbfffff : namespace1.0
```

Both namespaces are 2MiB \(0x200000\) aligned since namespace0.0 starts at 0x140000000 \(5GiB\) and namespace1.0 starts at 0x23fe00000 \(~9GiB\)

When creating filesystems using the namespaces, it’s important to maintain the 2MiB alignment \(4096 sectors\). Depending upon the VTOC type, fdisk creates 1MiB alignment \(2048 sectors\). For a non-device mapped /dev/pmem0 a partition aligned at the 2MiB boundary can be created using the following:

```text
$ fdisk /dev/pmem0

Welcome to fdisk (util-linux 2.32).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.

Device does not contain a recognized partition table.

Command (m for help): g
Created a new GPT disklabel (GUID: 10B97DA8-F537-6748-9E6F-ED66BBF7A047).

Command (m for help): n
Partition number (1-128, default 1):
First sector (2048-8249310, default 2048): 4096
Last sector, +sectors or +size{K,M,G,T,P} (4096-8249310, default 8249310):

Created a new partition 1 of type 'Linux filesystem' and of size 4 GiB.

Command (m for help): w
The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks.


$ fdisk -l /dev/pmem0
Disk /dev/pmem0: 4 GiB, 4223664128 bytes, 8249344 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 4096 bytes
I/O size (minimum/optimal): 4096 bytes / 4096 bytes
Disklabel type: gpt
Disk identifier: 10B97DA8-F537-6748-9E6F-ED66BBF7A047

Device       Start     End Sectors Size Type
/dev/pmem0p1  4096 8249310 8245215   4G Linux filesystem
```

3\) Create an XFS or EXT4 filesystem. The commands below show how this can be achieved. See the `mkfs.xfs` and `mkfs.ext4` man pages for more information.

EXT4:

```text
$ mkfs.ext4 -b 4096 -E stride=512 -F /dev/pmem0
$ mount /dev/pmem0 /mnt/dax
```

XFS:

```text
$ mkfs.xfs -f -d su=2m,sw=1 /dev/pmem0
$ mount /dev/pmem0 /mnt/dax
$ xfs_io -c "extsize 2m" /mnt/dax
```

4\) \[Optional\] Watch IO allocations. Without enabling filesystem debug options, it is possible to confirm the filesystem is allocating in 2MiB blocks using FTrace:

```text
$ cd /sys/kernel/debug/tracing
$ echo 1 > events/fs_dax/dax_pmd_fault_done/enable
```

Run test which faults in filesystem DAX mappings, eg:

```text
$ fallocate --length 1G /mnt/dax/data
```

Look for **dax\_pmd\_fault\_done** events in `/sys/kernel/debug/tracing/trace` to see if the allocations were successful. An event that successfully faulted in a filesystem DAX PMD looks like this:

```text
big-1434  [008] ....  1502.341229: dax_pmd_fault_done: dev 259:0 ino 0xc shared 
WRITE|ALLOW_RETRY|KILLABLE|USER address 0x10505000 vm_start 0x10200000 vm_end 
0x10600000 pgoff 0x305 max_pgoff 0x1400 NOPAGE
```

If the entry ends in **NOPAGE**, this means the fault succeeded and didn’t return a page cache page, which is expected for DAX. A 2 MiB fault that failed and fell back to 4 KiB DAX faults will instead look like this:

```text
small-1431  [008] ....  1499.402672: dax_pmd_fault_done: dev 259:0 ino 0xc shared
WRITE|ALLOW_RETRY|KILLABLE|USER address 0x10420000 vm_start 0x10200000 vm_end
0x10500000 pgoff 0x220 max_pgoff 0x3ffff FALLBACK
```

You can see that this fault resulted in a fallback to 4 KiB faults via the **FALLBACK** return code at the end of the line. The rest of the data in this line can help you determine why the fallback happened. In this example an intentional mmap\(\) smaller than 2 MiB was created. vm\_end \(0x10500000\) - vm\_start \(0x10420000\) == 0xE0000 \(896 KiB\).

To disable tracing run `echo 0 > events/fs_dax/dax_pmd_fault_done/enable` .

