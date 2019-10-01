# Show Device Platform Configuration Data

Shows the platform configuration data for one or more persistent memory modules.

```text
$ ipmctl show [OPTIONS] -dimm (DimmIds) -pcd (Config|LSA)
```

### **Targets**

* `-dimm (DimmIDs)`: Show the platform configuration data on specific modules by supplying the DIMM target and one or more comma-separated DimmIDs. The default is to display the platform configuration data for all manageable modules.
* `-pcd (Config|LSA)`: Restricts output to a specific partition of the platform configuration data. The default is to display both. One of:
  * `Config`: Configuration management information
  * `LSA`: Namespace label storage area

### **Examples** 

Show the configuration information from the platform configuration data for all manageable modules

```text
$ ipmctl show -dimm -pcd
```

Show the configuration information from the platform configuration data for module 0x0001

```text
ipmctl show -dimm 0x0001 -pcd Config
```

