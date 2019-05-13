# NDCTL User Guide

## Introduction

`ndctl` is a utility for managing the Linux LIBNVDIMM Kernel subsystem. It is designed to work with various non-volatile memory devices \(NVDIMMs\) from different vendors. The LIBNVDIMM subsystem defines a kernel device model and control message interface for platform NVDIMM resources like those defined by the [ACPI v6.0](http://www.uefi.org/sites/default/files/resources/ACPI_6_0_Errata_A.PDF) NFIT \(**N**VDIMM **F**irmware **I**nterface **T**able\). The latest [ACPI ](http://www.uefi.org/specifications)and [UEFI ](http://www.uefi.org/specifications)specifications can be found at [uefi.org](http://www.uefi.org). Operations supported by `ndctl` include:

* Provisioning capacity \(namespaces\)
* Enumerating Devices
* Enabling and Disabling NVDIMMs, Regions, and Namespaces
* Managing NVDIMM Labels

## Installing NDCTL

See [Installing NDCTL](../getting-started-guide/installing-ndctl.md) in the [Getting Started](../getting-started-guide/) Guide.

## Basic Usage

The `ndctl` command is designed to be user friendly. Once installed, a list of commands can be shown using any of the following:

1\) With no arguments or options, `ndctl` shows a simple usage message:

```text
# ndctl

 usage: ndctl [--version] [--help] COMMAND [ARGS]


 See 'ndctl help COMMAND' for more information on a specific command.
 ndctl --list-cmds to see all available commands

```

2\) Using `ndctl help` displays basic help and syntax:

```text
# ndctl help

 usage: ndctl [--version] [--help] COMMAND [ARGS]

 See 'ndctl help COMMAND' for more information on a specific command.
 ndctl --list-cmds to see all available commands
```

Below is an example of using the `ndctl help` command to launch the `create-namespace` man page:

```text
# ndctl help create-namespace
```

3\) Using `ndctl --list-cmds` lists all commands as a single list.

```text
# ndctl --list-cmds
version
enable-namespace
disable-namespace
create-namespace
destroy-namespace
check-namespace
clear-errors
enable-region
disable-region
enable-dimm
disable-dimm
zero-labels
read-labels
write-labels
init-labels
check-labels
inject-error
update-firmware
inject-smart
wait-scrub
start-scrub
setup-passphrase
update-passphrase
remove-passphrase
freeze-security
sanitize-dimm
load-keys
wait-overwrite
list
monitor
help
```

An alternative method for listing commands uses the [TAB key completion](./#tab-command-and-argument-completion) feature of ndctl.  By executing `ndctl <TAB> <TAB>` we can list the commands, eg:

```text
# ndctl <TAB> <TAB>
check-labels        disable-namespace   help                monitor             update-firmware     zero-labels
check-namespace     disable-region      init-labels         read-labels         update-passphrase
clear-errors        enable-dimm         inject-error        remove-passphrase   version
create-namespace    enable-namespace    inject-smart        sanitize-dimm       wait-overwrite
destroy-namespace   enable-region       list                setup-passphrase    wait-scrub
disable-dimm        freeze-security     load-keys           start-scrub         write-labels
```

### TAB Command and Argument Completion

`ndctl` supports command completion using the TAB key. For example, typing `ndctl enable-<TAB>` lists all commands beginning with 'enable', eg:

```text
# ndctl enable-<TAB>
enable-dimm        enable-namespace   enable-region
```

TAB completion also works with command arguments. For example, typing `ndctl enable-dimm <TAB>` will show all available command arguments. For example, the 'enable-dimm' command can enable one, more than one, or all NVDIMMs. It will list all available NVDIMMs \(nmem\) devices when using the TAB completion, eg:

```text
# ndctl enable-dimm <TAB>
all     nmem0
```

### Getting Help

NDCTL ships with a man page for each command. Each man page describes the required arguments and features in detail. Man pages can be found and accessed using the `man` or `ndctl` utilities.  The following `man -k ndctl` searches for any man page containing the "ndctl" keyword:

```text
# man -k ndctl
ndctl (1)            - Manage "libnvdimm" subsystem devices (Non-volatile Memory)
ndctl-check-labels (1) - determine if the given dimms have a valid namespace index block
ndctl-check-namespace (1) - check namespace metadata consistency
ndctl-clear-errors (1) - clear all errors (badblocks) on the given namespace
ndctl-create-namespace (1) - provision or reconfigure a namespace
ndctl-destroy-namespace (1) - destroy the given namespace(s)
ndctl-disable-dimm (1) - disable one or more idle dimms
ndctl-disable-namespace (1) - disable the given namespace(s)
ndctl-disable-region (1) - disable the given region(s) and all descendant namespaces
ndctl-enable-dimm (1) - enable one more dimms
ndctl-enable-namespace (1) - enable the given namespace(s)
ndctl-enable-region (1) - enable the given region(s) and all descendant namespaces
ndctl-freeze-security (1) - Set the given DIMM(s) to reject future security operations
ndctl-init-labels (1) - initialize the label data area on a dimm or set of dimms
ndctl-inject-error (1) - inject media errors at a namespace offset
ndctl-inject-smart (1) - perform smart threshold/injection operations on a DIMM
ndctl-list (1)       - dump the platform nvdimm device topology and attributes in json
ndctl-load-keys (1)  - load the kek and encrypted passphrases into the keyring
ndctl-monitor (1)    - Monitor the smart events of nvdimm objects
ndctl-read-labels (1) - read out the label area on a dimm or set of dimms
ndctl-remove-passphrase (1) - Stop a DIMM from locking at power-loss and requiring a passphrase to access media
ndctl-sanitize-dimm (1) - Perform a cryptographic destruction or overwrite of the contents of the given NVDIMM(s)
ndctl-setup-passphrase (1) - setup and enable the security passphrase for an NVDIMM
ndctl-start-scrub (1) - start an Address Range Scrub (ARS) operation
ndctl-update-firmware (1) - provides for updating the firmware on an NVDIMM
ndctl-update-passphrase (1) - update the security passphrase for an NVDIMM
ndctl-wait-overwrite (1) - wait for an overwrite operation to complete
ndctl-wait-scrub (1) - wait for an Address Range Scrub (ARS) operation to complete
ndctl-write-labels (1) - write data to the label area on a dimm
ndctl-zero-labels (1) - zero out the label area on a dimm or set of dimms
```

{% hint style="info" %}
Note: If `man -k ndctl` returns "ndctl: nothing appropriate." or similar, see the [Troubleshooting](troubleshooting.md#man-k-ndctl-returns-ndctl-nothing-appropriate) section to manually build the indexes.
{% endhint %}

Additionally, executing `ndctl help <command>` can be used to display the man page for the command, eg:

```text
# ndctl help enable-dimm
```

A list of ndctl man pages are available online. See '[NDCTL Man Pages](man-pages.md)' for a complete list.

## Displaying Bus, NVDIMM, Region, and Namespace Information

The `ndctl list` command is a very powerful and feature rich command. A list of options is shown below:

```text
# ndctl list -?
  Error: unknown switch `?'

 usage: ndctl list [<options>]

    -b, --bus <bus-id>    filter by bus
    -r, --region <region-id>
                          filter by region
    -d, --dimm <dimm-id>  filter by dimm
    -n, --namespace <namespace-id>
                          filter by namespace id
    -m, --mode <namespace-mode>
                          filter by namespace mode
    -t, --type <region-type>
                          filter by region-type
    -U, --numa-node <numa node>
                          filter by numa node
    -B, --buses           include bus info
    -D, --dimms           include dimm info
    -F, --firmware        include firmware info
    -H, --health          include dimm health
    -R, --regions         include region info
    -N, --namespaces      include namespace info (default)
    -X, --device-dax      include device-dax info
    -i, --idle            include idle devices
    -M, --media-errors    include media errors
    -u, --human           use human friendly number formats
```

Using the filters is a powerful way to limit the output.

### Examples

To list all active/enabled namespaces:

```text
# ndctl list -N
```

To list all active/enabled regions:

```text
# ndctl list -R
```

To list all active/enabled NVDIMMs:

```text
# ndctl list -D
```

To list all active/enabled NVDIMMs, Regions, and Namespaces:

```text
# ndctl list -DRN
```

To list all active/enabled and disabled/inactive \(idle\) NVDIMMs, Regions, and Namespaces:

```text
# ndctl list -DRNi
```

To list all active/enabled and disabled/inactive \(idle\) NVDIMMs, Regions, and Namespaces with human readable values:

```text
# ndctl list -iNuRD
```



