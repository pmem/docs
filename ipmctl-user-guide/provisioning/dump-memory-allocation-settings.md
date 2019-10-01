# Dump Memory Allocation Settings

Store the currently configured memory allocation settings for all persistent memory modules in the system to a file in order to replicate the configuration elsewhere. Apply the stored memory allocation settings using the command [Load Memory Allocation Goal](load-memory-allocation-goal.md)

```text
$ ipmctl dump [OPTIONS] -destination (path) -system -config
```

### **Example**

```text
$ ipmctl dump -destination config.txt -system -config
```

### **Limitations** 

Only memory allocation settings for manageable persistent memory modules that have been successfully applied by the BIOS are stored in the file. Unconfigured modules are not included, nor are memory allocation goals that have not been applied.

