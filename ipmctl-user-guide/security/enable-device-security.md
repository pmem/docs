# Enable Device Security

Enable data-at-rest security for the persistent memory on one or more persistent memory modules by setting a passphrase. For better passphrase protection, specify an empty string \(e.g., ConfirmPassphrase=""\) to be prompted for the passphrase or to use a file containing the passphrase with the source option.

```text
$ ipmctl set [OPTIONS] -dimm [TARGETS] NewPassphrase=(string) ConfirmPassphrase=(string)
```

### **Targets**

* `-dimm (DimmIDs)`

  Set the passphrase on specific modules by supplying one or more comma separated DimmIDs. However, this is not recommended as it may put the system in an undesirable state. The default is to set the passphrase on all manageable modules.

### **Properties**

* `NewPassphrase`: The new passphrase \(1-32 characters\).
* `ConfirmPassphrase`: Confirmation of the new passphrase \(1-32 character and must match NewPassphrase\).

### **Examples**

Set a passphrase on DIMM 0x0001

```text
$ ipmctl set -dimm 0x0001 NewPassphrase=123 ConfirmPassphrase=123
```

Set a passphrase on DIMM 0x0001 by supplying the passphrase in the file mypassphrase.file.

```text
ipmctl set -source mypassphrase.file -dimm 0x0001 NewPassphrase="" ConfirmPassphrase=""
```

In the previous example, the format of the file would be:

```text
#ascii
NewPassphrase=myNewPassphrase
```

### **Limitations**

In order to successfully execute this command:

* The caller must have the appropriate privileges. The specified module must have security disabled and be manageable by the host software.
* There must not be any goal creation pending.
* Command is subject to OS Vendor \(OSV\) support. If OSV does not provide support, command will return "Not Supported."

