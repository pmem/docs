# Show Memory Resources

Show the total Intel Optane persistent memory resource allocation across the host server.

```
ipmctl show [OPTIONS] -memoryresources
```

## **Examples**

### AppDirect

The following shows persistent memory module resource allocation from a system configured in 100% AppDirect mode:

```
$ ipmctl show -memoryresources
 MemoryType   | DDR         | PMemModule   | Total
==========================================================
 Volatile     | 381.500 GiB | 0.000 GiB    | 381.500 GiB
 AppDirect    | -           | 3024.000 GiB | 3024.000 GiB
 Cache        | 0.000 GiB   | -            | 0.000 GiB
 Inaccessible | 2.500 GiB   | 5.452 GiB    | 7.952 GiB
 Physical     | 384.000 GiB | 3029.452 GiB | 3413.452 GiB
```

### Human Readable Output

Show persistent memory module resource allocation with human-readable output (MiB and TiB).

```
# ipmctl show -units MiB -memoryresources
 MemoryType   | DDR            | PMemModule      | Total
===================================================================
 Volatile     | 390656.000 MiB | 0.000 MiB       | 390656.000 MiB
 AppDirect    | -              | 3096576.000 MiB | 3096576.000 MiB
 Cache        | 0.000 MiB      | -               | 0.000 MiB
 Inaccessible | 2560.000 MiB   | 5583.000 MiB    | 8143.000 MiB
 Physical     | 393216.000 MiB | 3102159.000 MiB | 3495375.000 MiB

 
 # ipmctl show -units TiB -memoryresources
 MemoryType   | DDR       | PMemModule | Total
===================================================
 Volatile     | 0.373 TiB | 0.000 TiB  | 0.373 TiB
 AppDirect    | -         | 2.953 TiB  | 2.953 TiB
 Cache        | 0.000 TiB | -          | 0.000 TiB
 Inaccessible | 0.002 TiB | 0.005 TiB  | 0.008 TiB
 Physical     | 0.375 TiB | 2.958 TiB  | 3.333 TiB
```

## **Return Data**

* `Volatile DDR Capacity`: Total DDR capacity that is used as volatile memory.
* `Volatile PMem module Capacity`: Total PMem module capacity that is used as volatile memory.
* `Total Volatile Capacity`: Total DDR and PMem module capacity that is used as volatile memory.
* `AppDirect PMem module Capacity`: Total PMem module capacity used as persistent memory.
* `Total AppDirect Capacity`: Total DDR and PMem module capacity used as persistent memory.
* `Cache DDR Capacity`: Total DDR capacity used as a cache for PMem modules.
* `Total Cache Capacity`: Total DDR capacity used as a cache for PMem modules.
* `Inaccessible DDR Capacity`: Total DDR capacity that is inaccessible.
* `InaccessibleCapacity`: Total system persistent memory capacity that is inaccessible due to any of:
  * Platform configuration prevents accessing this capacity. For example, MemoryCapacity is configured but MemoryMode is not enabled by platform FW (current Memory Mode is 1LM).
  * Capacity is inaccessible because it is not mapped into the System Physical Address space (SPA). This is usually due to platform firmware memory alignment requirements.
  * Persistent capacity that is reserved. This capacity is the persistent memory partition capacity (rounded down for alignment) less any App Direct capacity. Reserved capacity typically results from a Memory Allocation Goal request that specified the Reserved property. This capacity is not mapped to System Physical Address space (SPA).
  * Capacity that is unusable because it has not been configured.
  * PMem module configured capacity but SKU prevents usage. For example, AppDirectCapacity but PMem module SKU is MemoryMode only.
* `Total Inaccessible Capacity`: Total capacity of DDR and PMem module that is inaccessible.
* `Physical DDR Capacity`: Total physical DDR capacity populated on the platform.
* `Physical PMem module Capacity`: Total physical PMem module capacity populated on the platform.
* `Total Physical Capacity`: Total physical capacity populated on the platform.
