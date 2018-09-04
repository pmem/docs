# VMWare VSphere/ESXi

VMware VSphere v6.7 introduced a new 'PMem Datastore' which exposes persistent memory to a VM in two different modes.  PMem-aware VMs can have direct access to persistent memory.  Traditional VMs can use fast virtual disks stored on the PMem datastore.

For servers that do not have physical persistent memory DIMMs, VSphere has an option to experience the behavior of Persistent Memory using PMEM Emulator. The emulator uses a chunk of volatile memory and makes it behave like Persistent Memory. 

For more information please refer to the latest VMWare [VSphere documentation](https://docs.vmware.com/en/VMware-vSphere/).  

### Reference Material

* VMWare [Persistent Memory Initiative](https://code.vmware.com/persistent-memory-initiative)
* VMWare VSphere v6.7 '[Using Persistent Memory](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.storage.doc/GUID-93E5390A-8FCF-4CE1-8927-9FC36E889D00.html)' Documentation
* [VMware vSphere Virtualization of PMEM](https://www.youtube.com/watch?v=r5HLmt0GVRo) \[YouTube\] from the SNIA Persistent Memory Summit 2018



