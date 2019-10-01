# Provision Mixed Mode

Creates a memory allocation goal for a percentage \(0-100\) of total capacity to be used in Memory Mode and the rest to be either reserved or used for App Direct mode.

### **Examples**

Configure 20% of the available capacity on each persistent memory module to be in Memory Mode and the remaining will be not-interleaved App Direct.

```text
$ ipmctl create -goal MemoryMode=20 PersistentMemoryType=AppDirectNotInterleaved
```

Configure the persistent memory modules with 25% of the capacity in Memory Mode, 25% reserved, and the remaining 50% as interleaved App Direct by default.

```text
$ ipmctl create -goal MemoryMode=25 Reserved=25
```

