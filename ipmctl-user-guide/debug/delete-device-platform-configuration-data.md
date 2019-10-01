# Delete Device Platform Configuration Data

When `Config` is specified, the `Current`, `Input Data Size`, `Output Data Size` and `Start Offset` values in the Configuration header are set to zero, making those tables invalid. This action can be useful before moving modules from one system to another, as goal creation rules may restrict provisioning dimms with an existing configuration.

> Warning: This command may result in data loss. Data should be backed up to other storage before executing this command.

```text
$ ipmctl delete [OPTIONS] -dimm (DimmIds) -pcd (Config)
```

### **Targets**

* `-dimm (DimmIDs)`: Deletes the PCD data on specific persistent memory modules by supplying the DIMM target and one or more comma-separated DimmIDs. The default is to delete the PCD data for all manageable modules.
* `-pcd Config`: Clears the configuration management information

### **Examples** 

Clear the Cin, Cout, Ccur tables from all manageable modules

```text
$ delete -dimm -pcd Config
```

### **Limitations** 

The specified module\(s\) must be manageable by the host software, and if data-at-rest security is enabled, the modules must be unlocked. Any existing namespaces associated with the requested module\(s\) should be deleted before running this command.

