# Show Device

Shows information about one or more Intel Optane DC memory modules.

```text
ipmctl show [OPTIONS] -dimm [TARGETS]
```

### **Targets**

* `-dimm (DimmIDs)`: Restricts output to specific modules by supplying the DIMM target and one or more comma-separated DimmIDs. The default is to display all DCPMMs.
* `-socket (SocketIDs)`: Restricts output to the  modules installed on specific sockets by supplying the socket target and one or more comma-separated socket identifiers. The default is to display all sockets.
  * If ACPI PMTT table is not present, then DDR4 memory will not be displayed in the filtered socket list.

### **Examples** 

List a few key fields for each persistent memory module.

```text
$ ipmctl show -dimm

 DimmID | Capacity  | HealthState | ActionRequired | LockState | FWVersion
==============================================================================
 0x0001 | 126.4 GiB | Healthy     | 0              | Disabled  | 01.02.00.5310
 0x0011 | 126.4 GiB | Healthy     | 0              | Disabled  | 01.02.00.5310
 0x0021 | 126.4 GiB | Healthy     | 0              | Disabled  | 01.02.00.5310
 0x0101 | 126.4 GiB | Healthy     | 0              | Disabled  | 01.02.00.5310
 0x0111 | 126.4 GiB | Healthy     | 0              | Disabled  | 01.02.00.5310
 0x0121 | 126.4 GiB | Healthy     | 0              | Disabled  | 01.02.00.5310
 0x1001 | 126.4 GiB | Healthy     | 0              | Disabled  | 01.02.00.5310
 0x1011 | 126.4 GiB | Healthy     | 0              | Disabled  | 01.02.00.5310
 0x1021 | 126.4 GiB | Healthy     | 0              | Disabled  | 01.02.00.5310
 0x1101 | 126.4 GiB | Healthy     | 0              | Disabled  | 01.02.00.5310
 0x1111 | 126.4 GiB | Healthy     | 0              | Disabled  | 01.02.00.5310
 0x1121 | 126.4 GiB | Healthy     | 0              | Disabled  | 01.02.00.5310
```

List all properties for module 0x0001.

```text
$ ipmctl show -a -dimm 0x0001
```

Retrieve specific properties for each module.

```text
$ ipmctl show -d HealthState,LockState -dimm 0x0001

---DimmID=0x0001---
   LockState=Disabled
   HealthState=Healthy
```

### **Return Data**

* `HealthState` Overall module health. One of:
  * Healthy
  * Noncritical: Maintenance is required.
  * Critical: Features or performance are degraded due to failure.
  * Fatal: Data loss has occurred or is imminent. In this case, the firmware will disable the media and access to user data and operations that require use of the media will fail.
  * Non-functional: The module is present but is non-responsive via the DDRT communication path. It may be possible to communicate with this module via SMBus for a subset of commands.
  * Unmanageable: The module is not supported by this version of the software
  * Unknown: Unable to determine the module's health state.
* `ActionRequired`: If there are events for this device that require corrective action or acknowledgment. Refer to the command Show Events for more information about events. One of:
  * 0: No action required events
  * 1: One or more action required events
* `LockState` is the current security state of the persistent memory on the module. One of:
  * Unknown - The security state cannot be determined \(e.g., when the module is not manageable by the software\).
  * Disabled - Security is not enabled.
  * Disabled, Frozen - Security is not enabled. A reboot is required to change the security state.
  * Unlocked - Security is enabled and unlocked.
  * Unlocked, Frozen - Security is enabled and unlocked. A reboot is required to change the security state.
  * Locked - Security is enabled and locked.
  * Exceeded - The passphrase limit has been reached. A power cycle is required to change the security state.
  * Not Supported - Security is not supported on the module.

