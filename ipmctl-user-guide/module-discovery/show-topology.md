# Show Topology

Shows the topology of DDR and persistent memory modules installed in the host server.

```text
ipmctl show [OPTIONS] -topology [TARGETS]
```

### **Targets**

* `-dimm [(DimmIDs)]`: Restricts output to specific DIMMs by optionally supplying the DIMM target and one or more comma-separated DIMM identifiers. The default is to display all DIMMs.
* `-socket (SocketIDs)`: Restricts output to the DIMMs installed on specific sockets by supplying the socket target and one or more comma-separated socket identifiers. The default is to display all sockets.
  * If ACPI PMTT table is not present, then DDR4 memory will not be displayed in the filtered socket list.

### **Example**

```text
$ ipmctl show -topology

 DimmID | MemoryType                  | Capacity  | PhysicalID| DeviceLocator
==============================================================================
 0x0001 | Logical Non-Volatile Device | 126.3 GiB | 0x0028    | CPU1_DIMM_A2
 0x0011 | Logical Non-Volatile Device | 126.3 GiB | 0x002c    | CPU1_DIMM_B2
 0x0021 | Logical Non-Volatile Device | 126.3 GiB | 0x0030    | CPU1_DIMM_C2
 0x0101 | Logical Non-Volatile Device | 126.3 GiB | 0x0036    | CPU1_DIMM_D2
 0x0111 | Logical Non-Volatile Device | 126.3 GiB | 0x003a    | CPU1_DIMM_E2
 0x0121 | Logical Non-Volatile Device | 126.3 GiB | 0x003e    | CPU1_DIMM_F2
 0x1001 | Logical Non-Volatile Device | 126.3 GiB | 0x0044    | CPU2_DIMM_A2
 0x1011 | Logical Non-Volatile Device | 126.3 GiB | 0x0048    | CPU2_DIMM_B2
 0x1021 | Logical Non-Volatile Device | 126.3 GiB | 0x004c    | CPU2_DIMM_C2
 0x1101 | Logical Non-Volatile Device | 126.3 GiB | 0x0052    | CPU2_DIMM_D2
 0x1111 | Logical Non-Volatile Device | 126.3 GiB | 0x0056    | CPU2_DIMM_E2
 0x1121 | Logical Non-Volatile Device | 126.3 GiB | 0x005a    | CPU2_DIMM_F2
 N/A    | DDR4                        | 16.0 GiB  | 0x0026    | CPU1_DIMM_A1
 N/A    | DDR4                        | 16.0 GiB  | 0x002a    | CPU1_DIMM_B1
 N/A    | DDR4                        | 16.0 GiB  | 0x002e    | CPU1_DIMM_C1
 N/A    | DDR4                        | 16.0 GiB  | 0x0034    | CPU1_DIMM_D1
 N/A    | DDR4                        | 16.0 GiB  | 0x0038    | CPU1_DIMM_E1
 N/A    | DDR4                        | 16.0 GiB  | 0x003c    | CPU1_DIMM_F1
 N/A    | DDR4                        | 16.0 GiB  | 0x0042    | CPU2_DIMM_A1
 N/A    | DDR4                        | 16.0 GiB  | 0x0046    | CPU2_DIMM_B1
 N/A    | DDR4                        | 16.0 GiB  | 0x004a    | CPU2_DIMM_C1
 N/A    | DDR4                        | 16.0 GiB  | 0x0050    | CPU2_DIMM_D1
 N/A    | DDR4                        | 16.0 GiB  | 0x0054    | CPU2_DIMM_E1
 N/A    | DDR4                        | 16.0 GiB  | 0x0058    | CPU2_DIMM_F1
```

