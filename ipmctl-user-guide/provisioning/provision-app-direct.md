# Provision App Direct

Creates a memory allocation goal for App Direct in either one fully interleaved region, or non-interleaved, meaning one region per module.

**Examples**

```text
$ ipmctl create -goal PersistentMemoryType=AppDirect
```

```text
$ ipmctl create -goal PersistentMemoryType=AppDirectNotInterleaved
```

