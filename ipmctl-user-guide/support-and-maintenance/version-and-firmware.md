# Version and Firmware

### Get Version

Shows the persistent memory module host software versions

```text
$ ipmctl version
```

### Show Device Firmware

Shows detailed information about the firmware on one or more modules

```text
$ ipmctl show [OPTIONS] -firmware [TARGETS]
```

### Update Firmware 

Updates the firmware on one or more modules

```text
$ ipmctl load [OPTIONS] -source (path) -dimm (DimmIds) [TARGETS]
```

> NOTE: If Address Range Scrub \(ARS\) is in progress on any target DIMM, an attempt will be made to abort ARS and the proceed with the firmware update.
>
> NOTE: A reboot is required to activate the updated firmware image and is recommended to ensure ARS runs to completion.

#### **Examples** 

Update the firmware on all modules in the system to the image in sourcefile.pkg on the next power cycle.

```text
$ ipmctl load -source sourcefile.pkg -dimm
```

Check the firmware image in sourcefile.pkg and retrieve the version.

```text
$ ipmctl load -examine -source sourcefile.pkg -dimm
```

> NOTE: Once a firmware image is staged for execution, a power cycle is required before another firmware image of the same type \(production or debug\) can be staged for execution using this command.



