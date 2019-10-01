# Show System Capabilities

Shows the platform supported Intel Optane DC persistent memory capabilities across the server.

```text
ipmctl show [OPTIONS] -system -capabilities
```

### **Example**

```text
$ ipmctl show -system -capabilities

PlatformConfigSupported=1
Alignment=1.0 GiB
AllowedVolatileMode=Memory Mode
CurrentVolatileMode=1LM
AllowedAppDirectMode=App Direct
```

### **Return Data**

* `PlatformConfigSupported`: Whether the platform level configuration of persistent memory modules can be modified with the host software. One of:
  * 0: Changes must be made in the BIOS.
  * 1: The command Create Memory Allocation Goal is supported.
* `Alignment`: Capacity alignment requirement for all memory types as reported by the BIOS.
* `AllowedVolatileMode`: The volatile mode allowed as determined by BIOS setup. One of:
  * 1LM: One-level volatile mode. All memory resources in the platform are independently accessible, and not captive of the other resources.
  * Memory Mode: Modules act as system memory under the control of the operating system. In Memory Mode, any DDR in the platform will act as a cache working in conjunction with the persistent memory modules.
  * Unknown: The allowed volatile mode cannot be determined.
* `CurrentVolatileMode`: The current volatile mode. One of:
  * 1LM: One-level volatile mode. All memory resources in the platform are independently accessible, and not captive of the other resources.
  * Memory Mode: Modules act as system memory under the control of the operating system. In Memory Mode, any DDR in the platform will act as a cache working in conjunction with the persistent memory modules.
  * Unknown: The current volatile mode cannot be determined.
* `AllowedAppDirectMode`: The App Direct mode allowed as determined by BIOS setup. One of:
  * Disabled: App Direct support is currently disabled by the BIOS.
  * App Direct: App Direct support is currently enabled by the BIOS.
  * Unknown: The current App Direct support cannot be determined.

