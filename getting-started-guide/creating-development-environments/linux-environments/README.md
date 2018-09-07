# Linux Environments

## Emulating Persistent Memory NVDIMMs on Linux

Support for physical and emulated persistent memory devices, commonly referred to as Non-Volatile DIMMs \(NVDIMMs\), is present in the Linux Kernel v4.0 or newer. It is recommended to use Kernel version 4.2 or later since NVDIMM support is enabled by default. Kernel versions 4.0 and 4.1 require manual configuration and re-compiling the Kernel to enable support.

Linux offers several options to emulate persistent memory for development. The features and functionality vary for each option. The following table describes which features are available for each development environment.

|  | NVDIMM | Regions | Namespaces | FSDax | DevDax | Persistent Pools |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| [memmap Kernel Option](linux-memmap.md) | No | Yes | Yes | Yes | No | Yes |
| [QEMU Virtualization](../virtualization/qemu.md) | Yes | Yes | Yes | Yes | Yes | Yes |
| Docker Containers | No | No | No | Yes\* | Yes\* | Yes |
| [VMWare VSphere](../virtualization/vmware-vsphere-esxi.md) | Yes | Yes | Yes | Yes | Yes | Yes |

{% hint style="info" %}
Because emulation of persistent memory uses Volatile DRAM, the performance of emulated NVDIMMs will not match the performance of physical NVDIMMs. It is not recommended to rely on performance data when using emulated NVDIMMs.
{% endhint %}

{% hint style="info" %}
Data stored on emulated NVDIMMs will loose all the data when the system is power-cycled. Do not store critical data on emulated persistent memory.
{% endhint %}

\(\*\) Devices are passed through to the guest from the host. The guest cannot directly manage the devices.

