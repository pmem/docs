# Creating Development Environments

Providing developers and users access to specific environments is an important part of the application development lifecycle. Environments that support active development, testing, User Acceptance Testing \(UAT\), and production provide a full range of functionality.

Development environments can be created on servers with and without physical NVDIMMs installed. For servers without physical NVDIMMs, NVDIMM functionality can be emulated using volatile DDR memory. Several methods exist to create environments with emulated NVDIMMs. These are described in the following sections.

Application development using the PMDK can be done using traditional memory mapped files without emulating NVDIMMs. Such files can exist on any storage media. However, data consistency assurance embedded within PMDK requires frequent synchronization of data that is being modified. Depending on platform capabilities, and underlying device where the files are, a different set of commands is used to facilitate synchronization. It might be `msync(2)` for the regular hard drives, or combination of cache flushing instructions followed by memory fence instruction for the real persistent memory. Calling `msync` or `fsync` frequently can cause significant IO performance issues. For this reason, it is not recommended to use this approach for persistent memory application development.

The following sections describe how to create development environments using a variety of technologies for different operating systems:

Linux

* [Using the 'MEMMAP' Kernel Option](linux-environments/linux-memmap.md)

Windows

* [Windows Server 2016](windows-environments.md)

[Cloud Environments](cloud-environments.md)

Virtualization

* [Using QEMU Virtualization](virtualization/qemu.md)
* [Using VMWare VSphere/ESXi](virtualization/vmware-vsphere-esxi.md)

