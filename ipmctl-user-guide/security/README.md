# Security

Intel DC persistent memory modules support data-at-rest security by encrypting the data stored in the persistent regions of the DIMM. The CLI only supports transitioning to Disable Security State using the Change Device Security command.

> NOTE: Security commands are subject to OS Vendor \(OSV\) support and will return "Not Supported." An exception is if the module is in Unlocked Security State, then transitioning to Disabled is permitted.

For further security information, refer to the [Managing NVDIMM Security](../../ndctl-users-guide/managing-nvdimm-security.md) section of the NDCTL user guide.

