# NVDIMM Devices

A generic DIMM device object, named /dev/nmemX, is registered for each physical memory device indicated in the ACPI NFIT table, or other platform NVDIMM resource discovery mechanism. Physical NVDIMMs may have configuration options available via the BIOS. The features and available configuration options will be dependant on the NVDIMM device and BIOS.

The Linux LIBNVDIMM core provides a built-in driver for these DIMM devices. The driver is responsible for determining if the DIMM implements a namespace label area, and initializing the kernel's in-memory copy of that label data.

The kernel performs runtime modifications of that data when namespace provisioning actions are taken, and actively blocks user-space from initiating label data changes while the DIMM is active in any region. Disabling a DIMM, after all the regions it is a member of have been disabled, allows userspace to manually update the label data to be consumed when the DIMM is next enabled.

Refer to [Managing NVDIMMs](../managing-nvdimms.md) for more information on administering NVDIMMs.

