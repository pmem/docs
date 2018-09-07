# Namespaces

The capacity of an NVDIMM REGION \(contiguous span of persistent memory\) is accessed via one or more NAMESPACE devices. A REGION is the Linux term for what ACPI and UEFI call a DIMM-interleave-set, or a system-physical-address-range that is striped \(by the memory controller\) across one or more memory modules.

The UEFI specification defines the _NVDIMM Label Protocol_ as the combination of label area access methods and a data format for provisioning one or more NAMESPACE objects from a REGION. Note that label support is optional and if Linux does not detect the label capability it will automatically instantiate a "label-less" namespace per region. Examples of label-less namespaces are the ones created by the kernel's [memmap ](../../getting-started-guide/creating-development-environments/linux-environments/linux-memmap.md)option, or NVDIMMs without a valid _namespace index_ in their label area.

A namespace can be provisioned to operate in one of 4 modes, fsdax, devdax, sector, and raw:

* **fsdax:** Filesystem-DAX mode is the default mode of a namespace when specifying `ndctl create-namespace` with no options. It creates a block device \(/dev/pmemX\[.Y\]\) that supports the DAX capabilities of Linux filesystems \(xfs and ext4 to date\). DAX removes the page cache from the I/O path and allows mmap\(2\) to establish direct mappings to persistent memory media. The DAX capability enables workloads / working-sets that would exceed the capacity of the page cache to scale up to the capacity of persistent memory. Workloads that fit in page cache or perform bulk data transfers may not see benefit from DAX. When in doubt, pick this mode.
* **devdax:** Device-DAX mode enables similar mmap\(2\) DAX mapping capabilities as Filesystem-DAX. Instead of a block-device that can support a DAX enabled filesystem, this mode provides a single character device file \(/dev/daxX.Y\).  Use this mode to assign persistent memory to a virtual-machine, register persistent memory for RDMA, or when gigantic mappings are needed.
* **sector:** Use this mode to host legacy filesystems that do not checksum metadata or applications that are not prepared for torn sectors after a crash. Expected usage for this mode is for small boot volumes. This mode is compatible with other operating systems.
* **raw:** Raw mode is effectively just a memory disk that does not support DAX. Typically this indicates a namespace that was created by tooling or another operating system that did not know how to create a Linux fsdax or devdax mode namespace. This mode is compatible with other operating systems, but again, does not support DAX operation.

Figure 1 below shows a typical Filesystem-DAX \(FSDAX\) configuration with three physical NVDIMMs that have been interleaved. All available capacity has been used to create a single region \(Region0\), namespace \(Namespace0.0\), and dax enabled filesystem created. Namespace naming convention is commonly X.Y where X is the region number and Y is the namespace

![Figure 1: Common FSDAX Configuration](../../.gitbook/assets/draw.io-gitbook-interleaved-dimms-fsdax.jpg)

Refer to [Managing Namespaces](../managing-namespaces.md) for information on how to create, delete, edit, and manage the namespace configuration.

