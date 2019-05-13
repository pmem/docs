# Troubleshooting

The `ndctl` utility is designed to be user friendly and informative. This section describes some of the common messages seen when using `ndctl`.

## "DAX unsupported by block device. Turning off DAX."

**Issue:**

During system boot or mounting of an EXT4 or XFS filesystem, the following message may be seen in dmesg:

```text
[125.755367] XFS (pmem0): DAX enabled. Warning: EXPERIMENTAL, use at your own risk
[125.763736] XFS (pmem0): DAX unsupported by block device. Turning off DAX.
```

The filesystem should still mount, but it will not have the 'dax' \(Direct Access\) flag set as shown by the mount command:

```text
$ mount | grep pmem0
/dev/pmem0 on /mnt/pmem0 type xfs (rw,relatime,attr2,inode64,noquota)
```

**Solution:**

Check the namespace mode using `ndctl list -Nu`. In the following example the mode is 'raw':

```text
# ndctl list -Nu
{
  "namespaces":[
    {
      "dev":"namespace0.0",
      "mode":"raw",
      "size":"704.00 MiB (738.20 MB)",
      "blockdev":"pmem0",
      "numa_node":0
    }
  ]
}
```

From the `ndctl-create-namespace` man page, 'raw' mode does not support DAX \(Direct Access\).

```text
       -m, --mode=

           ·   "raw": expose the namespace capacity directly with limitations. Neither a raw pmem namepace nor raw blk
               namespace support sector atomicity by default (see "sector" mode below). A raw pmem namespace may have limited
               to no dax support depending the kernel. In other words operations like direct-I/O targeting a dax buffer may
               fail for a pmem namespace in raw mode or indirect through a page-cache buffer. See "fsdax" and "devdax" mode
               for dax operation.
```

The namespace needs to be changed to either 'fsdax' or 'devdax' using the following:

{% hint style="warning" %}
**WARNING:** Changing the namespace mode will destroy any existing data. Backup all data before changing the mode.
{% endhint %}

```text
$ sudo ndctl create-namespace -f -e namespace0.0 --mode=fsdax
{
  "dev":"namespace0.0",
  "mode":"memory",
  "size":"690.00 MiB (723.52 MB)",
  "uuid":"d1cc8945-7bf7-4b57-83b5-0a79565e9c15",
  "blockdev":"pmem0",
  "numa_node":0
}
```

The filesystem needs to be re-created, mounted with the 'dax' option, then any data restored:

```text
$ sudo mkfs.xfs /dev/pmem0
$ sudo mount -o dax /dev/pmem0 /mnt/pmem0
$ mount | grep pmem0
/dev/pmem0 on /mnt/pmem0 type xfs (rw,relatime,attr2,dax,inode64,noquota)
```

## "nmem0 is active, skipping..."

**Issue:**

When disabling an NVDIMM \(nmem\) device, a notice is displayed indicating it is active, eg:

```text
# ndctl disable-dimm nmem0
nmem0 is active, skipping...
disabled 0 nmem
```

**Cause:**

The message indicates there's at least one active/enabled Region and/or Namespace using the NVDIMM.

**Solution:**

All active/enabled Regions and Namespaces must be destroyed an/or disabled prior to disabling the dimm.

1\) List the current configuration:

```text
# ndctl list -NRD
```

2\) Verify no fsdax or devdax namespaces are mounted or in-use by running applications.

3\) Destroy or disable the namespace\(s\).

4\) Disable the regions used by the NVDIMM \(nmem\) that needs to be disabled.

5\) Disable the NVDIMM \(nmem\).

## "ndctl: error while loading shared libraries: libjson-c.so.2: cannot open shared object file: No such file or directory"

**Issue:**

Executing the `ndctl` utility, with or without commands or options, yields the following missing library error:

```text
# ndctl --version
ndctl: error while loading shared libraries: libjson-c.so.2: cannot open shared object file: No such file or directory
```

**Cause:**

The issue could be caused by one of the following issues:

* The json-c package is not installed
* A version mis-match between the `json-c` and `ndctl`.  More recent versions of json-c deliver `libjson-c.so.4` rather than `libjson-c.so.2`. 
* The json-c library is installed in a non-default location and the LD\_LIBRARY\_PATH environment variable needs to be updated.

Verify which libraries are missing from `ndctl` using the `ldd` utility and identifying libraries that are 'not found':

```text
# ldd `which ndctl`
        linux-vdso.so.1 (0x00007ffc677c4000)
        libndctl.so.6 => /lib64/libndctl.so.6 (0x00007f31fca49000)
        libdaxctl.so.1 => /lib64/libdaxctl.so.1 (0x00007f31fc843000)
        libuuid.so.1 => /lib64/libuuid.so.1 (0x00007f31fc63c000)
        libjson-c.so.2 => not found
        libc.so.6 => /lib64/libc.so.6 (0x00007f31fc27d000)
        libudev.so.1 => /lib64/libudev.so.1 (0x00007f31fc057000)
        [...]
```

**Solution:**

Verify that the json-c package is installed. For example, on Fedora use `dnf info --installed json-c`. If it is installed, information about the package will be displayed, eg:

```text
# sudo dnf info --installed json-c
Installed Packages
Name         : json-c
Version      : 0.13.1
Release      : 2.fc28
Arch         : x86_64
Size         : 65 k
Source       : json-c-0.13.1-2.fc28.src.rpm
Repo         : @System
From repo    : updates
Summary      : JSON implementation in C
URL          : https://github.com/json-c/json-c
License      : MIT
Description  : JSON-C implements a reference counting object model that allows you
             : to easily construct JSON objects in C, output them as JSON formatted
             : strings and parse JSON formatted strings back into the C representation
             : of JSON objects.  It aims to conform to RFC 7159.
```

If the package is not installed, a message similar to the following will be returned:

```text
# sudo dnf info --installed json-c
Error: No matching Packages to list
```

To install the json-c package if it is missing, use

```text
# sudo dnf install json-c
```

To query the json-c package to identify the library version use the following:

```text
# dnf repoquery -l json-c | grep libjson-c.so
Last metadata expiration check: 2:07:37 ago on Fri 06 Jul 2018 06:18:47 AM MDT.
/usr/lib64/libjson-c.so.4
/usr/lib64/libjson-c.so.4.0.0
/usr/lib64/libjson-c.so.4
/usr/lib64/libjson-c.so.4.0.0
/usr/lib/libjson-c.so.4
/usr/lib/libjson-c.so.4.0.0
/usr/lib/libjson-c.so.4
/usr/lib/libjson-c.so.4.0.0
```

Verify the LD\_LIBRARY\_PATH includes the location of libjson-c.so.\*. Note: `/usr/lib` and `/usr/lib64` are automatically included.

```text
# echo $LD_LIBRARY_PATH
/usr/local/lib:/usr/local/lib64
```

If the package and LD\_LIBRARY\_PATH are correct, the version of ndctl will need to be updated. Using `ndctl --version` won't work and will simply return "_ndctl: error while loading shared libraries: libjson-c.so.2: cannot open shared object file: No such file or directory_".

If the `ndctl` utility was installed using the ndctl package from the operating system's repository, update the package to the latest version. On Fedora:

```text
# sudo dnf update -y ndctl
```

If the latest version within the package repository is old with no new versions available, download, compile, and install from source code. Detailed instructions can be found in the [Installing NDCTL](../getting-started-guide/installing-ndctl.md) chapter.

If the `ndctl` utility was previously compiled and installed using source code, download the latest version from the [ndctl GitHub repository](https://github.com/pmem/ndctl), compile, and install. Detailed instructions can be found in the [Installing NDCTL](../getting-started-guide/installing-ndctl.md) chapter.

## Error: namespace0.0 is active, specify --force for re-configuration

**Issue:**

Destroying a namespace mode may fail with the following error:

```text
# ndctl destroy-namespace namespace0.0
  Error: namespace0.0 is active, specify --force for re-configuration
```

Changing a namespace mode may fail with the following error:

```text
# ndctl create-namespace -e namespace0.0 -m fsdax
  Error: namespace0.0 is active, specify --force for re-configuration

failed to reconfigure namespace: Device or resource busy
```

**Cause:**

The error indicates the namespace is currently **active** and potentially mounted \(FSDAX\) or in use, so the operation is not permitted.

**Solution:**

1\) Verify the current namespace mode using `ndctl list -N` to show all namespaces or filter the result by specifying namespace name using `ndctl list -N namespace{X.Y}`, eg:

```text
# ndctl list -N
{
  "dev":"namespace0.0",
  "mode":"raw",
  "size":134217728000,
  "uuid":"0a66b46c-5146-45bb-a0f1-320f28db4d40",
  "sector_size":512,
  "blockdev":"pmem0",
  "numa_node":0
}
```

If the namespace mode is 'fsdax', verify any associated filesystems are unmounted.

If the namespace mode is 'devdax', verify any application using the device is stopped.

{% hint style="info" %}
The 'fuser' command on Linux can be used to identify any running processes with the /dev/dax\* or /dev/pmem\* devices open.
{% endhint %}

2\) Either disable the namespace prior to deleting it or changing the mode or use the `-f`, `--force` options to override the active status checks.

To change the mode of an active namespace without disabling it first, use the `-f`, `--force` option:

```text
# ndctl create-namespace -f -e namespace0.0 -m fsdax
{
  "dev":"namespace0.0",
  "mode":"fsdax",
  "map":"dev",
  "size":"123.04 GiB (132.12 GB)",
  "uuid":"1cf25e33-8dfa-4ad2-b409-e236d598bc1e",
  "raw_uuid":"9368c235-26d3-4220-9048-e85f3ccd8469",
  "sector_size":512,
  "blockdev":"pmem0",
  "numa_node":0
}
```

To delete a namespace without disabling it first, use the `-f`, `--force` option:

```text
# ndctl destroy-namespace -f namespace0.0
destroyed 1 namespace
```

Alternatively, disable the namespace then perform the destroy/mode change operation. Note: Changing a namespace mode automatically activates it.

To change the namespace mode from 'raw' to 'fsdax':

```text
# ndctl list -N
{
  "dev":"namespace0.0",
  "mode":"raw",
  "size":134217728000,
  "uuid":"ce15f005-d3bd-4a94-8565-55be77bed6f6",
  "sector_size":512,
  "blockdev":"pmem0",
  "numa_node":0
}

# ndctl disable-namespace namespace0.0
disabled 1 namespace

# ndctl create-namespace -e namespace0.0 -m fsdax
{
  "dev":"namespace0.0",
  "mode":"fsdax",
  "map":"dev",
  "size":"123.04 GiB (132.12 GB)",
  "uuid":"f45a6725-b80e-4174-a366-b07c91e66a58",
  "raw_uuid":"ee359fec-0a83-436a-91b6-44e3f45b04e0",
  "sector_size":512,
  "blockdev":"pmem0",
  "numa_node":0
}
```

To destroy a namespace:

```text
# ndctl list -N
{
  "dev":"namespace0.0",
  "mode":"fsdax",
  "map":"dev",
  "size":132118478848,
  "uuid":"f45a6725-b80e-4174-a366-b07c91e66a58",
  "raw_uuid":"ee359fec-0a83-436a-91b6-44e3f45b04e0",
  "sector_size":512,
  "blockdev":"pmem0",
  "numa_node":0
}

# ndctl disable-namespace namespace0.0
disabled 1 namespace

# ndctl destroy-namespace namespace0.0
destroyed 1 namespace
```

## "failed to create namespace: Resource temporarily unavailable"

**Issue:**

Creating a new namespace using 'ndctl create-namespace', may return the following error:

```text
# ndctl create-namespace -r region0
failed to create namespace: Resource temporarily unavailable
```

**Cause:**

There are many potential causes including:

* There's no available capacity within the region because one or more namespaces exist and have consumed all the space.
* The region is disabled.
* There's an issue with the labels for the NVDIMMs belonging to the region.

**Solution:**

Use the `-v` option to print more information to help identify the cause. A debug version of `ndctl` may be required to get useful information. See [Installing NDCTL](../getting-started-guide/installing-ndctl.md) for instructions to build `ndctl` with debug options from source code.

For a scenario where there's no space left within the region, a message similar to the following will be shown:

```text
# ndctl create-namespace -r region0 -v
...
namespace_create:772: region0: insufficient capacity size: 0 avail: 0
failed to create namespace: Resource temporarily unavailable
```

When a region is disabled, the following will be shown:

```text
# ndctl create-namespace -r region0 -v
...
validate_namespace_options:473: region0: disabled, skipping...
failed to create namespace: Resource temporarily unavailable
```

Identifying label issues requires more investigation. Start with [Validating Labels](managing-label-storage-areas-lsa.md#checking-labels).

The `Resource temporarily unavailable` message improvements is being handled by [https://github.com/pmem/ndctl/issues/67](https://github.com/pmem/ndctl/issues/67)

## "man -k ndctl" returns "ndctl: nothing appropriate."

**Issue:**

When searching the short descriptions and manual page for a _keyword_, the man utility may return an empty set, for example:

```text
# man -k ndctl
ndctl: nothing appropriate.
```

**Cause:**

If you built ndctl from source, the man page indexes may not have been automatically generated for you.  Additionally, if you installed ndctl to a non-default location, your $MANPATH shell environment variable may not been updated to point to to the new man page locations.

**Solution:**

1\) Verify the current system global search paths for manual pages

```text
# manpath -g
/usr/man:/usr/share/man:/usr/local/man:/usr/local/share/man:/usr/X11R6/man:/opt/man
```

2\) Identify if these paths are overridden by a local MANPATH setting, for example the following shows no additional search paths

```text
# echo $MANPATH

#
```

3\) If the install location of ndctl is not included in the above output, add the path to your MANPATH shell environment, otherwise skip this step:

```text
# export MANPATH=$MANPATH:<path_to_ndctl_man_pages>
```

4\) Manually scan and build the man page indexes

```text
# sudo mandb -c
```

This command may take a few minutes depending on the number of search paths and man pages it has to index.  The output is verbose as it scans and indexes.  Once complete, it will provide a summary:

```text
...
118 man subdirectories contained newer manual pages.
10550 manual pages were added.
0 stray cats were added.
```

5\) If the ndctl man pages were scanned and indexed, `man -k ndctl` will now work as expected, eg:

```text
# man -k ndctl
ndctl (1)            - Manage "libnvdimm" subsystem devices (Non-volatile Memory)
ndctl-check-labels (1) - determine if the given dimms have a valid namespace index block
ndctl-check-namespace (1) - check namespace metadata consistency
ndctl-clear-errors (1) - clear all errors (badblocks) on the given namespace
...
```

