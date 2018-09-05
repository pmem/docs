# Virtualization

This section describes how support for physical and emulated persistent memory devices, commonly referred to as Non-Volatile DIMMs \(NVDIMMs\), is provided through various virtualization and hypervisor technologies.  The table below shows the supported features of each technology.

|  | NVDIMM | Regions | Namespaces | FSDax | DevDax | Persistent Pools |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| [QEMU Virtualization](qemu.md) | Yes | Yes | Yes | Yes | Yes | Yes |
| Docker Containers | No | No | No | Yes\* | Yes\* | Yes |
| [VMWare VSphere](vmware-vsphere-esxi.md) | Yes | Yes | Yes | Yes | Yes | Yes |



