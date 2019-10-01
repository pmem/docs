# Create Memory Allocation Goal

```text
ipmctl create [OPTIONS] -goal [TARGETS] [PROPERTIES]
```

The `ipmctl create -goal` command has many options. A complete list of options can be shown by executing `ipmctl create -help`, or reading the `ipmctl(1)` man page. **Once a goal is created, it does not take effect until the system is rebooted.** After a reboot, the BIOS configures the requested goal, and clears the goal. 

### **Targets**

* `-dimm [(DimmIDs)]`: Creates a memory allocation goal on specific persistent memory modules by optionally supplying one or more comma-separated DimmIDs. The default is to configure all manageable modules on all sockets.
* `socket (SocketIds)`: Creates a memory allocation goal on specific modules by supplying one or more comma-separated SocketIds. The default is to configure all manageable modules on all sockets.

### **Properties**

* `MemoryMode`: Percentage of the total capacity to use in Memory Mode \(0-100\). Default=0.
* `PersistentMemoryType`: If MemoryMode is not 100%, specify the type of persistent memory to create:
  * `AppDirect`: \(default\) Create App Direct capacity utilizing hardware interleaving across the requested modules.
  * `AppDirectNotInterleaved`: Create App Direct capacity that is not interleaved with any other modules.
* `NamespaceLabelVersion`: The version of the namespace label storage area \(LSA\) index block:
  * `1.2`: \(default\) Defined in UEFI 2.7a - sections 13.19
  * `1.1`: Legacy 1.1 namespace label support
* `Reserved`: Reserve a percentage \(0-100\) of the requested persistent memory App Direct capacity that will not be mapped into the system physical address space and will be presented as `ReservedCapacity` with Show Device and Show Memory Resources commands.

### **Limitations**

* The caller must have appropriate privileges.
* The specified modules must be manageable by the host software and must all have the same SKU.
* Existing memory allocation goals that have not been applied and any namespaces associated with the requested modules must be deleted before running this command.

