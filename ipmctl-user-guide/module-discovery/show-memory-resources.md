# Show Memory Resources

Show the total Intel Optane DC persistent memory resource allocation across the host server.

```text
ipmctl show [OPTIONS] -memoryresources
```

### **Example**

Show persistent memory module resource allocation.

```text
$ ipmctl show -memoryresources

Capacity=1517.0 GiB
MemoryCapacity=0.0 GiB
AppDirectCapacity=1512.0 GiB
UnconfiguredCapacity=0.0 GiB
InaccessibleCapacity=5.0 GiB
ReservedCapacity=0.0 GiB
```

### **Return Data**

* `MemoryCapacity`: Total usable system persistent memory for Memory Mode.
* `AppDirectCapacity`: Total usable system persistent memory for App Direct mode.
* `UnconfiguredCapacity`: Total system persistent memory capacity that is unusable because it has not been configured.
* `ReservedCapacity`: Total system persistent memory capacity that is reserved. This capacity is the persistent memory partition capacity \(rounded down for alignment\) less any App Direct capacity. Reserved capacity typically results from a Memory Allocation Goal request that specified the Reserved property. This capacity is not mapped to system physical address \(SPA\) space.
* `InaccessibleCapacity`: Total system persistent memory capacity that is inaccessible due any of:
  * Platform configuration prevents accessing this capacity. e.g. MemoryCapacity is configured but MemoryMode is not enabled by platform FW \(current operating mode is 1LM\).
  * Capacity is inaccessible because it is not mapped into the system physical address space \(SPA\). This is usually due to platform firmware memory alignment requirements.
  * Intel Optane DC persistent memory is configured but SKU prevents usage. e.g. AppDirectCapacity but  SKU is MemoryMode only.

