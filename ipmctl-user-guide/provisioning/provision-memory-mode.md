# Provision Memory Mode

Creates a memory allocation goal for a percentage \(0-100\) of total capacity to be used in Memory Mode and the rest to be either reserved or used for App Direct mode.

### **Examples**

Configure 100% of the available capacity on each persistent memory module to be in Memory Mode.

```text
$ ipmctl create -goal MemoryMode=100
```

By default, if a goal is set for any percentage less than 100%, the remaining capacity will be configured to App Direct mode. See the [Mixed Mode](provision-mixed-mode.md) section for examples. 

