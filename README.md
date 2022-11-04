# Persistent Memory Documentation

## **Introductory Materials**

This [USENIX ;login: article](https://www.usenix.org/system/files/login/articles/login\_summer17\_07\_rudoff.pdf), published in the Summer of 2017, provides an overview of the persistent memory programming model and the related software work that has been taking place. It includes a very high-level description of programming with [PMDK](http://pmem.io/pmdk/), although it refers to it by the old name, [NVM Libraries](http://pmem.io/2017/12/11/NVML-is-now-PMDK.html). This article is actually a follow-on to an [earlier article](https://www.usenix.org/system/files/login/articles/08\_rudoff\_040-045\_final.pdf), published in the Summer 2013 issue of USENIX ;login:.

The [Persistent Memory Summit 2018](https://www.snia.org/pm-summit), held January 24th, 2018, provided a day full of informative talks and panels on persistent memory (slides and videos of the sesssions are available at above link).

The projects on this site, like [PMDK](http://pmem.io/pmdk/), are product-neutral, meant to work on any product that provides the persistent memory programming model. One such product is Intel’s [3D XPoint](https://www.youtube.com/watch?v=Wgk4U4qVpNY). Intel has assembled a collection of persistent memory programming documentation, webinars, videos, and related materials on the [Intel Developer Zone](https://software.intel.com/en-us/persistent-memory).

Dozens of companies have collaborated on a common persistent memory programming model in the SNIA NVM Programming Technical Workgroup (TWG). SNIA provides a [persistent memory page](http://www.snia.org/PM) on their web site with links to recent presentations, persistent memory standards and white papers, etc. The SNIA activity continues to be very active, working on areas like remote persistent memory and security. The original programming model specification put forward the basic idea that applications access persistent memory as _memory-mapped files_, a concept which has appeared as the **DAX** feature (Direct Access) on both [Linux](https://nvdimm.wiki.kernel.org/) and [Windows](https://channel9.msdn.com/Events/Build/2016/P470).

## **Standards**

The [SNIA NVM Programming Model](https://www.snia.org/sites/default/files/technical\_work/final/NVMProgrammingModel\_v1.2.pdf) standard describes the basic programming model used for persistent memory programming.

The [ACPI Specification](http://www.uefi.org/specifications), starting with version 6.0, defines the _NVDIMM Firmware Interface Table_ (NFIT) which is how the existence of persistent memory is communicated to operating systems. The specification also describes how NVDIMMs are partitioned into _namespaces_, methods for communicating with NVDIMMs, etc.

The [UEFI Specification](http://www.uefi.org/specifications), covers other NVDIMM-related topics such as the _Block Translation Table_ (BTT) which allows an NVDIMM to provide block device semantics.

Before the above two standards were developed, the [NVDIMM Namespace Specification](http://pmem.io/documents/NVDIMM\_Namespace\_Spec.pdf) described the namespace and BTT mechanisms. The link to the outdated document is maintained here for reference.

## **Related Specifications**

The [NVDIMM Driver Writers Guide](http://pmem.io/documents/NVDIMM\_DriverWritersGuide-July-2016.pdf) is targeted to driver writers for NVDIMMs that adhere to the NFIT tables in the Advanced Configuration and Power Interface (ACPI) V6.0 specification, the Device Specific Method (DSM) specification, and the NVDIMM Namespace Specification. This document specifically discusses the block window HW interface and persistent memory interface that Intel is proposing for NVDIMMs. A version of the document with [change bars](http://pmem.io/documents/NVDIMM\_DriverWritersGuide-July-2016\_wChanges.pdf) \[pdf] from the previous version is also available.

The [Intel Optane PMem DSM Interface](https://pmem.io/documents/IntelOptanePMem\_DSM\_Interface-V3.0.pdf), Version 3.0, describes the NVDIMM Device Specific Methods (\_DSM) that pertain to Optane PMem modules. This document is provided as a reference for BIOS and OS driver writers supporting NVDIMMs and similar devices that appear in the ACPI NFIT table.

The [Dirty Shutdown Handling guide](https://pmem.io/documents/Dirty\_Shutdown\_Handling-V1.0.pdf) describes the (hopefully rare) error case where flushes to persistent memory did not go as expected on power failure or system crash.  The guide describes how this failure is detected, and how it is communicated to applications via the dirty shutdown count**.**
