# Change Device Security

Changes the data-at-rest security lock state for the persistent memory on one or more persistent memory modules.

```text
$ ipmctl set [OPTIONS] -dimm [TARGETS] Lockstate=(Unlocked|Disabled|Frozen) Passphrase=(string)
```

### **Targets**

* `-dimm (DimmIDs)`: Changes the lock state of a specific module by supplying one or more comma separated DimmIDs. However, this is not recommended as it may put the system in an undesirable state. The default is to modify all manageable DCPMMs.

### **Properties**

* `LockState`: The desired lockstate
  * `Disabled`: Removes the passphrase on a module to disable security. Permitted only when LockState is Unlocked.
  * `Unlocked`: Unlocks the persistent memory on a locked module.
  * `Frozen`: Prevents further lock state changes to the module until the next reboot.
* `Passphrase`: The current passphrase \(1-32 characters\).

### **Examples** 

Unlock device 0x0001

```text
$ ipmctl set -dimm 0x0001 LockState=Unlocked Passphrase=""
```

Unlock device 0x0001 by supplying the passphrase in the file "mypassphrase.file".

```text
ipmctl set -source myfile.file -dimm 0x0001 LockState=Unlocked Passphrase=""
```

In the previous example, the file would be:

```text
#ascii
Passphrase=myPassphrase
```

### **Limitations** 

To successfully execute this command:

* The caller must have the appropriate privileges and the specified modules must be manageable by the host software, meaning:
  * have security enabled
  * not be in the `unlocked`, `frozen`, or `Exceeded` lock states
  * not be executing a long operation \(ARS, Overwrite, FWUpdate\)
* The command is subject to OS Vendor \(OSV\) support. If the OSV does not provide support, the command may return "Not Supported." An exception is if the module is Unlocked \(via UEFI or OSV tools\), then transitioning to Disabled is possible regardless of OSV support.

