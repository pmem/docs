# Module Discovery

Persistent memory modules are uniquely referenced by one of two IDs: `DimmHandle` or `DimmUID`. Either ID may be used for commands that utilize the `-dimm` flag to perform operations on a single module or set of modules. The default is to operate on all modules, which does not require the use of `-dimm`.

For example, each of the following are equivalent:

```
    $ ipmctl show -d DimmHandle,DimmUID -dimm 8089-a2-1748-00000001
    $ ipmctl show -d DimmHandle,DimmUID -dimm 0x0001
    $ ipmctl show -d DimmHandle,DimmUID -dimm 1
```

For simplicity, this document will primarily use `DimmUID`.

The `-dimm` option accepts a single DimmUID or a comma separated list of DimmUIDs to filter the results. For example, the following `ipmctl show` command displays the `DimmHandle` and `DimmUID` properties for two modules with IDs of `0x0001` and `0x1001`:

```
    $ ipmctl show -d DimmHandle,DimmUID -dimm 0x0001,0x1001

    ---DimmID=0x0001---
     DimmHandle=0x0001
     DimmUID=8089-a2-1748-00000001
    ---DimmID=0x1001---
     DimmHandle=0x1001
     DimmUID=8089-a2-1748-00000002
```

## DimmHandle

The `DimmHandle` is equivalent to the `DimmUID` and is formatted as 0xABCD, where A, B, C, and D are defined as follows:

* A = Socket
* B = Memory Controller
* C = Channel
* D = Slot

## DimmUID

The `DimmUID` is a unique identifier specific to each physical module. The unique identifier of an Intel Optane persistent memory module is formatted as VVVV-ML-MMYYSNSNSNSN or VVVV-SNSNSNSN (if the manufacturing information is not available) where:

* VVVV = VendorID
* ML = Manufacturing Location
* MMYY = Manufacturing Date
* SNSNSNSN = Serial Number
