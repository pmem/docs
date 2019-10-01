# Show Memory Allocation Goal

Shows the memory allocation goal on one or more persistent memory modules. Once the goal is successfully applied by the BIOS, it is no longer displayed. Use the command Show Memory Resources to view the system-wide memory resources.

```text
ipmctl show [OPTIONS] -goal [TARGETS] [PROPERTIES]
```

**Targets**

* `-dimm [(DimmIDs)]`: Restricts output to specific DIMMs by optionally supplying the DIMM target and one or more comma-separated DIMM identifiers. The default is to display all manageable persistent memory modules with memory allocation goals.
* `-socket (SocketIDs)`: Restricts output to the DIMMs installed on specific sockets by supplying the socket target and one or more comma-separated socket identifiers. The default is to display all sockets with memory allocation goals.

**Examples**

```text
$ ipmctl show -goal
```

```text
$ ipmctl show -goal -socket 1
```

**Return Data**

* `SocketID`: The processor socket identifier where the module is installed.
* `DimmID`: The persistent memory module identifier.
* `MemorySize`: The module capacity that will be configured in Memory Mode.
* `AppDirect1Size`: The persistent module capacity that will be configured as the first App Direct interleave set if applicable.
* `AppDirect2Size`: The persistent memory module capacity that will be configured as the second App Direct interleave set if applicable.
* `Status`: The status of the memory allocation goal. One of:
  * `Unknown`: The status cannot be determined.
  * `New`: A reboot is required for the memory allocation goal to be processed by the BIOS.
  * `Failed - Bad Request`: The BIOS failed to process the memory allocation goal because it was invalid.
  * `Failed - Not enough resources`: There were not enough resources for the BIOS to process the memory allocation goal.
  * `Failed - Firmware error`: The BIOS failed to process the memory allocation goal due to a firmware error.
  * `Failed - Unknown`: The BIOS failed to process the memory allocation goal due to an unknown error.

