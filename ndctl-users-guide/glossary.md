# Glossary

**BLK:** Block Mode: A set of one or more programmable memory mapped apertures provided by an NVDIMM to access its media. This indirection precludes the performance benefit of interleaving, but enables NVDIMM-bounded failure modes.

**BTT:** Block Translation Table: Persistent memory is byte addressable. Existing software may have an expectation that the power-fail-atomicity of writes is at least one sector, 512 bytes. The BTT is an indirection table with atomic update semantics to front a PMEM/BLK block device driver and present arbitrary atomic sector sizes.

**DAX:** Direct Access File system extensions to bypass the page cache and block layer to mmap persistent memory, from a PMEM block device, directly into a process address space.

**DCR:** NVDIMM Control Region Structure: It defines a vendor-id, device-id, and interface format for a given DIMM. See [ACPI v6.0](http://www.uefi.org/sites/default/files/resources/ACPI_6_0_Errata_A.PDF) Section 5.2.25.5.

**DPA:** DIMM Physical Address: is an NVDIMM-relative offset. With one NVDIMM in the system there would be a 1:1 system-physical-address:DPA association. Once more NVDIMMs are added a memory controller interleave must be decoded to determine the DPA associated with a given system-physical-address. BLK capacity always has a 1:1 relationship with a single-NVDIMM's DPA range.

**DSM:** Device Specific Method: ACPI method to to control specific device - in this case the firmware.

**LABEL:** Metadata stored on an NVDIMM device that partitions and identifies \(persistently names\) storage between PMEM and BLK. It also partitions BLK storage to host BTTs with different parameters per BLK-partition. Note that traditional partition tables, GPT/MBR, are layered on top of a BLK or PMEM device.

**NVDIMM:** A Non-Volatile DIMM device \(also called a module\) installed in to a memory socket on the system board. Data is written to the device and stored persistently, meaning data is retained across power-cycles.

**PMEM:** Persistent Memory Mode: A system-physical-address range where writes are persistent. A block device composed of PMEM is capable of DAX. A PMEM address range may span an interleave of several NVDIMMs.

