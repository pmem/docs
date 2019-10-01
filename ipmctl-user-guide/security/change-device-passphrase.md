# Change Device Passphrase

Changes the security passphrase on one or more persistent memory modules. For better passphrase protection, specify an empty string \(e.g., Passphrase=""\) to be prompted for the current passphrase or to use a file containing the passphrases with the source option.

```text
ipmctl set [OPTIONS] -dimm [TARGETS] Passphrase=(string) NewPassphrase=(string) ConfirmPassphrase=(string)
```

### **Targets**

* `-dimm (DimmIDs)`: Changes the passphrase on specific module by supplying one or more comma separated persistent memory module identifiers. However, this is not recommended as it may put the system in an undesirable state. The default is to change the passphrase on all manageable modules.

### **Properties**

* `Passphrase`: The current passphrase \(1-32 characters\).
* `NewPassphrase`: The new passphrase \(1-32 characters\).
* `ConfirmPassphrase`: Confirmation of the new passphrase \(1-32 character and must match NewPassphrase\).

### **Examples** 

Change the passphrase from `mypassphrase` to `mynewpassphrase` on all modules.

```text
$ ipmctl set -dimm Passphrase=mypassphrase NewPassphrase=mynewpassphrase ConfirmPassphrase=mynewpassphrase
```

Change the passphrase on all modules by having the CLI prompt for the current and new passphrases.

```text
$ ipmctl set -dimm Passphrase="" NewPassphrase="" ConfirmPassphrase=""
```

Change the passphrase on all modules by supplying the current and new passphrases from the specified file.

```text
$ ipmctl set -source passphrase.file -dimm Passphrase="" NewPassphrase="" ConfirmPassphrase=""
```

In the previous example, the format of the file would be:

```text
#ascii
Passphrase=myOldPassphrase
NewPassphrase=myNewPassphrase
```

### **Limitations**

* The specified module must be manageable by the host software, have security enabled and not be in the "Unlocked, Frozen", "Disabled, Frozen", or "Exceeded" lock states.
* Command is subject to OS Vendor \(OSV\) support. If OSV does not provide support, command will return "Not Supported."

