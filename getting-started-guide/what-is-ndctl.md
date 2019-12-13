# NDCTL Introduction

The Non-Volatile Device Control \(ndctl\) is a utility for managing the LIBNVDIMM Linux Kernel subsystem. The LIBNVDIMM subsystem defines a kernel device model and control message interface for platform NFIT \(NVDIMM Firmware Interface Table\). This interface was first defined by the [ACPI v6.0 specification](http://www.uefi.org/sites/default/files/resources/ACPI_6.0.pdf). Later versions may enhance or modify this specification. The latest ACPI and UEFI specifications can be found at [http://uefi.org/specifications](http://uefi.org/specifications).

The LIBNVDIMM subsystem provides support for three types of NVDIMMs, namely, PMEM, BLK, and NVDIMM devices that can simultaneously support both PMEM and BLK mode access. These three modes of operation are described by the "NVDIMM Firmware Interface Table" \(NFIT\) in ACPI v6.0 or later. While the LIBNVDIMM implementation is generic and supports pre-NFIT platforms, it was guided by the superset of capabilities need to support this ACPI 6 definition for NVDIMM resources. The bulk of the kernel implementation is in place to handle the case where DPA accessible via PMEM is aliased with DPA accessible via BLK. When that occurs a LABEL is needed to reserve DPA for exclusive access via one mode a time.

Operations supported by the tool include provisioning capacity \(namespaces\), as well as enumerating/enabling/disabling the devices \(DIMMs, regions, namespaces\) associated with an NVDIMM bus.

## Get Started

To get started with the ndctl utility, first follow the [Installing NDCTL](installing-ndctl.md) document then the [NDCTL Users Guide]() and [man pages]().

## **Supporting Documents**

* NDCTL Project Homepage: [http://pmem.io/ndctl/](http://pmem.io/ndctl/)
* LIBNVDIMM Documentation: [https://www.kernel.org/doc/Documentation/nvdimm/nvdimm.txt](https://www.kernel.org/doc/Documentation/nvdimm/nvdimm.txt)
* ACPI v6.0: [http://www.uefi.org/sites/default/files/resources/ACPI\_6.0.pdf](http://www.uefi.org/sites/default/files/resources/ACPI_6.0.pdf) 
* Latest ACPI & UEFI Specifications: [http://uefi.org/specifications](http://uefi.org/specifications)
* NVDIMM Namespace: [http://pmem.io/documents/NVDIMM\_Namespace\_Spec.pdf](http://pmem.io/documents/NVDIMM_Namespace_Spec.pdf) 
* DSM Interface Specification \(v1.8\): [http://pmem.io/documents/NVDIMM\_DSM\_Interface-V1.8.pdf](http://pmem.io/documents/NVDIMM_DSM_Interface-V1.8.pdf)
* DSM Interface Example: [http://pmem.io/documents/NVDIMM\_DSM\_Interface\_Example.pdf](http://pmem.io/documents/NVDIMM_DSM_Interface_Example.pdf) 
* Driver Writer's Guide: [http://pmem.io/documents/NVDIMM\_Driver\_Writers\_Guide.pdf](http://pmem.io/documents/NVDIMM_Driver_Writers_Guide.pdf)

**Source Code Repositories**

* LIBNVDIMM: [https://git.kernel.org/cgit/linux/kernel/git/djbw/nvdimm.git](https://git.kernel.org/cgit/linux/kernel/git/djbw/nvdimm.git) 
* LIBNDCTL: [https://github.com/pmem/ndctl.git](https://github.com/pmem/ndctl.git) 
* PMEM: [https://github.com/01org/prd](https://github.com/01org/prd)

