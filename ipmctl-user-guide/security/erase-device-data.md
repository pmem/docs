# Erase Device Data

Erases the persistent data on one or more module.

```text
$ ipmctl delete [OPTIONS] -dimm [TARGETS] Passphrase=(string)
```

### **Targets**

* `-dimm (DimmIDs)`: Erases specific module device data by supply one or more comma-separated DimmIDs.

### **Properties**

* `Passphrase`: The current passphrase \(1-32 characters\). If security state is disabled, then passphrase is not required and will be ignored if supplied. If security state is enabled, then a passphrase must be supplied.

### **Examples**

Security disabled modules: Erase all persistent data on all modules in the system

```text
$ ipmctl delete -dimm
```

Security enabled specifics: Erase all the persistent data on all modules in the system

```text
$ ipmctl delete -dimm Passphrase=123
```

Erase all the persistent data on all modules using the CLI prompt for the current passphrase.

```text
$ ipmctl delete -dimm Passphrase=""
```

### **Limitations**

* The specified module must be manageable by the host software, have security enabled and not be in the "Unlocked, Frozen", "Disabled, Frozen", or "Exceeded" lock states.
* Command is subject to OS Vendor \(OSV\) support. If the OSV does not provide support, command will return "Not Supported."

