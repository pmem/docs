# Load Memory Allocation Goal

Load a memory allocation goal from a file onto one or more persistent memory modules.

> **WARNING**: Provisioning or changing modes may result in data loss. Data should be backed up to other storage before executing this command.
>
> > Changing a memory allocation goal modifies how the platform firmware maps persistent memory in the system address space \(SPA\), which may result in data loss or inaccessible data, but does not explicitly delete or modify user data found in persistent memory.

```text
$ ipmctl load [OPTIONS] -source (path) -goal [TARGETS]
```

### **Targets**

* `-dimm [(DimmIDs)]`: Restricts output to specific DIMMs by optionally supplying the DIMM target and one or more comma-separated DIMM identifiers. The default is to display all manageable persistent memory modules.
* `-socket (SocketIDs)`: Restricts output to the DIMMs installed on specific sockets by supplying the socket target and one or more comma-separated socket identifiers. The default is to display all sockets.

### **Examples**

Load the configuration settings stored in "config.txt" onto all modules in the system as a memory allocation goal to be applied by the BIOS on the next reboot.

```text
$ ipmctl load -source config.txt -goal
```

Load the configuration settings stored in "config.txt" onto modules 1, 2, and 3 in the system as a memory allocation goal to be applied by the BIOS on the next reboot.

```text
$ ipmctl load -source config.txt -goal -dimm 1,2,3
```

Load the configuration settings stored in "config.txt" onto all manageable modules on sockets 1 and 2 as a memory allocation goal to be applied by the BIOS on the next reboot.

```text
$ ipmctl load -source config.txt -goal -socket 1,2
```

### **Limitations**

* The caller must have appropriate privileges.
* The specified modules must be manageable by the host software and must all have the same SKU.
* Existing memory allocation goals that have not been applied and any namespaces associated with the requested modules must be deleted before running this command.

