# Provision App Direct

Creates a memory allocation goal for App Direct in either one fully interleaved region, or non-interleaved, meaning one region per module.

## **Examples**

Configures all the PMem module capacity in **** AppDirect mode with all modules in an interleaved set:

```
$ ipmctl create -goal PersistentMemoryType=AppDirect
```

Configures all the PMem module capacity in **** AppDirect mode with all modules in an interleaved set:

```
$ ipmctl create -goal PersistentMemoryType=AppDirectNotInterleaved
```

Configures the PMem module capacity across the entire system with 50% in AppDirect (Interleaved) and 50% reserved:

```
ipmctl create -goal PersistentMemoryType=AppDirect Reserved=50
```

Create an Interleaved AppDirect goal using all modules in Socket0:

```
$ ipmctl create -goal -socket 0x0000 PersistentMemoryType=AppDirect
```
