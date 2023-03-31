# Create Memory Allocation Goal

```
ipmctl create [OPTIONS] -goal [TARGETS] [PROPERTIES]
```

The `ipmctl create -goal` command has many options. A complete list of options can be shown by executing `ipmctl create -help`, or reading the `ipmctl(1)` man page. Once a goal is created, it does not take effect until the system is rebooted. After a reboot, the BIOS configures the requested goal and clears the goal.

## **Targets**

* `-dimm [(DimmIDs)]`: Creates a memory allocation goal on specific persistent memory modules by optionally supplying one or more comma-separated DimmIDs. The default is to configure all manageable modules on all sockets.
* `-socket (SocketIds)`: Creates a memory allocation goal on specific modules by supplying one or more comma-separated SocketIds. The default is to configure all manageable modules on all sockets.

## **Properties**

* `MemoryMode`: Percentage of the total capacity to use in Memory Mode (0-100). Default=0.
* `PersistentMemoryType`: If MemoryMode is not 100%, specify the type of persistent memory to create:
  * `AppDirect`: (default) Create App Direct capacity utilizing hardware interleaving across the requested modules.
  * `AppDirectNotInterleaved`: Create App Direct capacity that is not interleaved with any other modules.
* `NamespaceLabelVersion`: The version of the namespace label storage area (LSA) index block:
  * `1.2`: (default) Defined in UEFI 2.7a - sections 13.19
  * `1.1`: Legacy 1.1 namespace label support
* `Reserved`: Reserve a percentage (0-100) of the requested persistent memory App Direct capacity that will not be mapped into the system physical address space and will be presented as `ReservedCapacity` with Show Device and Show Memory Resources commands.

## **Limitations**

* The caller must have appropriate privileges.
* The specified modules must be manageable by the host software and must all have the same SKU.
* Existing memory allocation goals that have not been applied and any namespaces associated with the requested modules must be deleted before running this command.

## Examples

Configures all the PMem module capacity in Memory Mode:

```
ipmctl create -goal MemoryMode=100
```

Configures all the PMem module capacity as App Direct:

```
ipmctl create -goal PersistentMemoryType=AppDirect
```

Configures the capacity on each PMem module with 20% of the capacity in Memory Mode and the remaining as App Direct capacity that does not use hardware interleaving:

```
ipmctl create -goal MemoryMode=20 PersistentMemoryType=AppDirectNotInterleaved
```

Configures the PMem module capacity across the entire system with 50% in AppDirect (Interleaved) and 50% reserved:

```
ipmctl create -goal PersistentMemoryType=AppDirect Reserved=50
```

Configures the PMem module capacity across the entire system with 25% of the capacity in Memory Mode, 25% reserved, and the remaining 50% as App Direct. Configures the PMem module capacity across the entire system with 25% of the capacity in Memory Mode and the remaining 75% as App Direct.

```
ipmctl create -goal MemoryMode=25 PersistentMemoryType=AppDirect Reserved=25
```

Create an Interleaved AppDirect goal using all modules in Socket0:

```
$ ipmctl create -goal -socket 0x0000 PersistentMemoryType=AppDirect
```
