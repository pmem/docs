# Delete Memory Allocation Goal

Deletes the memory allocation goal from one or more persistent memory modules. This command deletes a memory allocation goal request that has not yet been processed by the BIOS.

```text
$ ipmctl delete [OPTIONS] -goal [TARGETS]
```

### **Targets**

* `-dimm [(DimmIDs)]`: Restricts output to specific DIMMs by optionally supplying the DIMM target and one or more comma-separated DIMM identifiers. The default is to display all manageable modules.
* `-socket (SocketIDs)`: Restricts output to the DIMMs installed on specific sockets by supplying the socket target and one or more comma-separated socket identifiers. The default is to display all sockets.

### **Example**

```text
$ ipmctl delete -goal
```

