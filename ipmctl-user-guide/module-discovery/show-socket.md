# Show Socket

Shows basic information about the physical processors in the host server.

```text
ipmctl show [OPTIONS] -socket [TARGETS]
```

### **Targets**

* `-socket (SocketIDs)`: Restricts output to the DIMMs installed on specific sockets by supplying the socket target and one or more comma-separated socket identifiers. The default is to display all sockets.

### **Examples** 

Display information about all the processors.

```text
$ ipmctl show -socket

 SocketID | MappedMemoryLimit | TotalMappedMemory
==================================================
 0x0000   | 4608.0 GiB        | 851.0 GiB
 0x0001   | 4608.0 GiB        | 850.5 GiB
```

List all properties for socket 1.

```text
 ipmctl show -socket 1

 SocketID | MappedMemoryLimit | TotalMappedMemory
==================================================
 0x0001   | 4608.0 GiB        | 850.5 GiB
```

Retrieve specific properties for each processor.

```text
$ ipmctl show -d MappedMemoryLimit -socket

---SocketID=0x0000---
   MappedMemoryLimit=4608.0 GiB
---SocketID=0x0001---
   MappedMemoryLimit=4608.0 GiB
```

### **Return Data**

* The `MappedMemoryLimit` is the maximum amount of memory that is allowed to be mapped into the system physical address space for this processor based on its SKU.
* `TotalMappedMemory` is the total amount of memory that is currently mapped into the system physical address space for this processor.

