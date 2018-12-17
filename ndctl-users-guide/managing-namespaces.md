# Managing Namespaces

Regions can be partitioned in to one or more namespaces. Namespace operations include listing, creating, destroying, enabling, disabling, and checking \(validating\) using the following commands:

* [list ](managing-namespaces.md#listing-namespaces)- dump the platform nvdimm device topology and attributes in json format 
* [create-namespace](managing-namespaces.md#creating-namespaces) - provision or reconfigure a namespace 
* [destroy-namespace](managing-namespaces.md#destroying-namespaces) - destroy the given namespace\(s\) 
* [disable-namespace](managing-namespaces.md#disabling-namespaces) - disable the given namespace\(s\) 
* [enable-namespace](managing-namespaces.md#enabling-namespaces) - enable the given namespace\(s\) 
* [check-namespace](managing-namespaces.md#checking-sector-namespaces) - check namespace metadata consistency

## Listing Namespaces

#### Displaying Active/Enabled Namespaces

The `ndctl list -N` command is used to display namespaces, for example:

```text
# ndctl list -N
{
  "dev":"namespace0.0",
  "mode":"fsdax",
  "map":"mem",
  "size":4294967296,
  "sector_size":512,
  "blockdev":"pmem0",
  "numa_node":0
}
```

The output can be filtered using any combination of the following options:

```text
       -n, --namespace=
           An namespaceX.Y device name, or namespace region plus id tuple X.Y. Limit the namespace list to the single
           identified device if present.

       -m, --mode=
           Filter listing by the mode (raw, fsdax, sector or devdax) of the namespace(s).
```

**Examples:**

List all active namespaces

```text
# ndctl list -N
```

List all active namespaces and print human readable values \(MB, MiB, GB, GiB, etc\)

```text
# ndctl list -Nu
{
  "dev":"namespace0.0",
  "mode":"fsdax",
  "map":"mem",
  "size":"4.00 GiB (4.29 GB)",
  "sector_size":512,
  "blockdev":"pmem0",
  "numa_node":0
}
```

List a single namespace \(namespace0.0\)

```text
# ndctl list -n namespace0.0
```

List all active 'fsdax' mode namespaces

```text
# ndctl list -m fsdax
```

#### Displaying Disabled/Inactive Namespaces

By default, `ndctl list -N` lists only active/enabled namespaces. In the following example no active/enabled namespaces exist so no output is displayed:

```text
# ndctl list -N
#
```

Adding the `-i` flag includes both active/enabled and inactive/destroyed/disabled namespaces. The following example shows two previously destroyed namespaces. The `size` and `uuid` are clear indications that no information exists about the namespace. The `ndctl destroy` command simply NULLs the existing namespace entry and does not completely erase it from the metadata. For this reason, it may be possible to see more than one namespace using `ndctl list -Ni`

```text
# ndctl list -Ni
[
  {
    "dev":"namespace0.0",
    "mode":"raw",
    "size":0,
    "uuid":"00000000-0000-0000-0000-000000000000",
    "sector_size":512,
    "state":"disabled",
    "numa_node":0
  }
]
```

The `-i` option can be used in coordination with any other valid ndctl option.

**Examples:**

List all enabled and disabled namespaces

```text
# ndctl list -Ni
```

List a single disabled namespace \(namespace0.0\)

```text
# ndctl list -in namespace0.0
```

List all enabled and disabled 'fsdax' mode namespaces

```text
# ndctl list -im fsdax
```

## Creating Namespaces

The create-namespace command has a lot of options summarized below. The `ndctl-create-namespace` man page contains further details of each option.

* **-t, --type:** Set the type of the namespace to either "pmem" or "blk"
* **-m, mode:** Define the namespace mode - fsdax, devdax, sector, and raw
* **-s, --size:** Specify the capacity of the namespace
* **-a, --align:** Specify the alignments size, eg 4K or 2M
* **-e, --reconfigure:** Reconfigure existing namespace configuration
* **-u, --uuid:** Used for recovery purposes only
* **-n, --name:** Specify a friendly name for namespaces that support labels
* **-l, --sector-size:** Override the default sector size for block based namespaces
* **-M, --map:** For 'fsdax' or 'devdax' namespaces, define whether metadata is stored in volatile memory \(mem\) or persistent storage \(dev\)
* **-f, --force:** Allow the operation to continue on enabled namespaces
* **-L, --autolabel, --no-autolabel:** Manage labels for legacy NVDIMMs that do not support labels
* **-v, --verbose:** Emit verbose messages during the creation process
* **-r, --region:** Limit the operation to a specific region
* **-b, --bus:** Limit the operation to a specific bus

The `mode` is the most important feature to get correct. The four modes available are defined as:

* **fsdax:** Filesystem-DAX mode is the default mode of a namespace when specifying `ndctl create-namespace` with no options. It creates a block device \(/dev/pmemX\[.Y\]\) that supports the DAX capabilities of Linux filesystems \(xfs and ext4 to date\). DAX removes the page cache from the I/O path and allows mmap\(2\) to establish direct mappings to persistent memory media. The DAX capability enables workloads / working-sets that would exceed the capacity of the page cache to scale up to the capacity of persistent memory. Workloads that fit in page cache or perform bulk data transfers may not see benefit from DAX. When in doubt, pick this mode.
* **devdax:** Device-DAX mode enables similar mmap\(2\) DAX mapping capabilities as Filesystem-DAX. However, instead of a block-device that can support a DAX-enabled filesystem, this mode emits a single character device file \(/dev/daxX.Y\). Use this mode to assign persistent memory to a virtual-machine, register persistent memory for RDMA, or when gigantic mappings are needed.
* **sector:** Use this mode to host legacy filesystems that do not checksum metadata or applications that are not prepared for torn sectors after a crash. Expected usage for this mode is for small boot volumes. This mode is compatible with other operating systems.
* **raw:** Raw mode is effectively just a memory disk that does not support DAX. Typically this indicates a namespace that was created by tooling or another operating system that did not know how to create a Linux fsdax or devdax mode namespace. This mode is compatible with other operating systems, but again, does not support DAX operation.

{% hint style="info" %}
If the _-s, --size_ option is used, the value provided to ndctl is used to create the namespace. Depending on the _mode_, the available capacity may be smaller than the specified size due to the calculated spared required for metadata. This is shown below in the examples.
{% endhint %}

### FSDAX and DEVDAX Capacity Considerations

The namespace configuration \(label metadata\) is stored at the beginning of the persistent memory address range. See the [Label Storage Area \(LSA\)](concepts/label-area.md) and [Managing Label Storage Area \(LSA\)](managing-label-storage-areas-lsa.md) sections for more information.

In addition to the namespace label metadata, both fsdax and devdax modes require space to store the Page Frame Number \(PFN\) metadata \(also known as “struct page” metadata\) for each page within the namespace. A PFN is simply in index within physical memory, or on the NVDIMM, that is counted in page-sized units. For modes with PFN metadata, shown in Table 1 below, the requirement is 64 bytes per 4 KiB of persistent memory. To calculate the estimated space for metadata, use:

$$
metadata _{(bytes)} = (size _{(bytes)}/4096)*64
$$

**Example 1:** A 256GiB namespace requires ~3.93GiB of metadata

\(263182090240/4096\)\*64 = 4112220160 bytes == 3.93GB

**Example 2:** A 1TiB namespace requires ~17.18GiB of metadata

\(1099511627776/4096\)\*64 = 17179869184 bytes == 17.18GiB

The location of PFN metadata can either be in volatile DDR from the NVDIMM. The default is to keep metadata on the NVDIMM. The location is defined per namespace by the `-M, --map` configuration option. The following provides guidance for PFN metadata location:

* **map=mem:** PFN metadata is stored in regular system memory \(DDR\)
  * Adequate for small persistent memory capacities
  * Frequent or high performance PFN access is required.  For example, when time sensitive, or frequent, registration and deregistration of RDMA memory regions is important.  Normal RDMA memory \[de\]registration can be achieved with 'map=dev', but there are situations where \[de\]registration needs to be very fast.  This assumes the metadata fits in DDR memory.
* **map=dev:** PFN metadata is stored on persistent memory \(NVDIMM\)
  * Intended for large persistent memory capacities.  There might not be enough regular memory in the system if the ratio of persistent memory to DDR is high \(&gt;8:1\)

The persistence of the PFN metadata is not important as it's re-mapped at system boot time or when new namespaces are created/destroyed. It is convenient to store the metadata on the NVDIMM because it scales with the persistent memory capacity. The space for metadata is calculated and reserved at namespace creation time, meaning the usable capacity is lower than the physical capacity depending on the mode and map values.

**Table 1: Features for each namespace mode**

| Mode | Description | Device Path | Label Metadata | Atomicity | Filesystems | DAX | PFN Metadata |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| raw | raw | /dev/pmemN | no | no | yes | no | no |
| sector | sector atomic | /dev/pmemNs | yes | yes | yes | no | no |
| fsdax | filesystem DAX | /dev/pmemN | yes | no | yes | yes | yes |
| devdax | device DAX | /dev/daxN.M | yes | no | no | yes | yes |

**PFN Metadata Usage Example**

The following demonstrates the difference between `--map=dev` and `--map=mem`. The example uses a ~250GiB region to create an fsdax namespace. A devdax namespace has identical behavior.

When a namespace using the default `--map=dev` is created, the usable 'size' is ~4GiB smaller than the 249GiB region size due to the space reserved for PFN metdata.

```text
# ndctl create-namespace -r region0
# ndctl list -RuN -r region0
{
  "regions":[
    {
      "dev":"region0",
      "size":"249.00 GiB (267.36 GB)",
      "available_size":0,
      "type":"pmem",
      "iset_id":"0xb000000058000000",
      "persistence_domain":"memory_controller",
      "namespaces":[
        {
          "dev":"namespace0.0",
          "mode":"fsdax",
          "map":"dev",
          "size":"245.11 GiB (263.18 GB)",
          "uuid":"19cccf5d-d227-42fe-b338-cfa882a6b6ce",
          "blockdev":"pmem0"
        }
      ]
    }
  ]
}
```

When using `--map=mem`, the usable capacity on the namespace increases, but the amount of 'used' memory as shown by `free` has increased by ~4GiB.

```text
# free
              total        used        free      shared  buff/cache   available
Mem:      362926556     5747588   356160304        2608     1018664   355200308
Swap:      32882684           0    32882684

# ndctl create-namespace -r region0 --map=mem
# ndctl list -RuN -r region0
{
  "regions":[
    {
      "dev":"region0",
      "size":"249.00 GiB (267.36 GB)",
      "available_size":0,
      "type":"pmem",
      "iset_id":"0xb000000058000000",
      "persistence_domain":"memory_controller",
      "namespaces":[
        {
          "dev":"namespace0.0",
          "mode":"fsdax",
          "map":"mem",
          "size":"249.00 GiB (267.36 GB)",
          "uuid":"ff626073-eef9-422c-9b09-814fc1498c1b",
          "blockdev":"pmem0"
        }
      ]
    }
  ]
}

# free
              total        used        free      shared  buff/cache   available
Mem:      362926556     9827632   352080120        2612     1018804   351120192
Swap:      32882684           0    32882684
```

#### **Creating Namespace Examples**

This example section has been split in to four, one for each mode described above:

* [FSDAX Mode Examples](managing-namespaces.md#fsdax-mode-examples)
* [DEVDAX Mode Examples](managing-namespaces.md#devdax-mode-examples)
* [SECTOR Mode Examples](managing-namespaces.md#sector-mode-examples)
* [RAW Mode Examples](managing-namespaces.md#raw-mode-examples)

#### **FSDAX Mode Examples**

**Example 1:** Create an 'fsdax' mode namespace using all available capacity \(default behavior\).

The `ndctl create-namespace` command shown below is the equivalent of `ndctl create-namespace -m fsdax`. A new `/dev/pmem{X[.Y]}` device is created. This can be used to create a new DAX enabled filesystem such as XFS or EXT4. The 'X' value represents the region on which the namespace is created. This defaults to zero \(0\). If more than one namespace is created within a region, the naming convention of pmem{X.Y} is used, where Y represents a sequentially increasing integer value for the new namespace. If multiple namespaces within a region are created, the first namespace is always created as 'pmem0', it does not get re-enumerated to 'pmem0.0' like subsequent namespaces.

```text
# ndctl create-namespace
{
  "dev":"namespace0.0",
  "mode":"fsdax",
  "map":"dev",
  "size":"123.04 GiB (132.12 GB)",
  "uuid":"a60e6a4f-274d-4cd5-8d39-c8dd263345e2",
  "raw_uuid":"8d28948f-5434-4c8d-8efa-581eacad265a",
  "sector_size":512,
  "blockdev":"pmem0",
  "numa_node":0
}

# ls -l /dev/pmem*
brw-rw----. 1 root disk 259, 0 Jul  9 10:42 /dev/pmem0
```

**Example 2:** Create a 50GiB 'fsdax' mode namespace.

The 'size' provided to ndctl includes space required for metadata so the resulting available capacity for a filesystem will be smaller as the example below shows:

```text
# ndctl create-namespace -m fsdax -s 50G
{
  "dev":"namespace0.0",
  "mode":"fsdax",
  "map":"dev",
  "size":"49.22 GiB (52.85 GB)",
  "uuid":"638d67f3-4c18-4b2e-a6f2-a044bdc82253",
  "raw_uuid":"6c6dfdad-e12f-418d-9fb0-3a75032dd9de",
  "sector_size":512,
  "blockdev":"pmem0",
  "numa_node":0
}
```

**Note:** If the remaining capacity needs to be assigned to another namespace using the same or different mode, the remaining capacity can be assigned to the new namespace without specifying the '-s &lt;size&gt;' option. For example, executing `ndctl create-namespace` after the command above creates a new namespace in the same region using all remaining capacity \(~78GiB\):

```text
# ndctl create-namespace
{
  "dev":"namespace0.1",
  "mode":"fsdax",
  "map":"dev",
  "size":"73.83 GiB (79.27 GB)",
  "uuid":"d7f9473e-97aa-48cf-aefa-128797c83e88",
  "raw_uuid":"6c116e57-19dd-43d8-ae03-039f1588a23a",
  "sector_size":512,
  "blockdev":"pmem0.1",
  "numa_node":0
}
```

**Example 3:** Create a namespace with a friendly name \(tag\)

Tagging the namespace with a friendly name or description using the '-n, --name' option can be useful to know what a namespace is used for. This is particularly useful when provisioning multiple end-users, applications, or to identify a namespace with production or test/dev environments. Creating a namespace with a tag/name/description can be done using:

```text
# ndctl create-namespace -n "PROD Web DB 1"
{
  "dev":"namespace0.0",
  "mode":"fsdax",
  "map":"dev",
  "size":"123.04 GiB (132.12 GB)",
  "uuid":"6a0abb59-5279-4731-921a-0099101e17f2",
  "raw_uuid":"03b40e23-56e1-407a-b5d1-f1ec929645c1",
  "sector_size":512,
  "blockdev":"pmem0",
  "name":"PROD Web DB 1",
  "numa_node":0
}
```

#### **DEVDAX Mode Examples**

Create a pmem namespace with devdax mode using all available capacity

```text
# ndctl create-namespace -m devdax
{
  "dev":"namespace0.0",
  "mode":"devdax",
  "map":"dev",
  "size":"123.04 GiB (132.12 GB)",
  "uuid":"e1c1d031-71a3-4a6b-b3d7-a4037a5256c3",
  "raw_uuid":"2a15b77f-9f31-45e5-84a3-4ad06817302a",
  "daxregion":{
    "id":0,
    "size":"123.04 GiB (132.12 GB)",
    "align":2097152,
    "devices":[
      {
        "chardev":"dax0.0",
        "size":"123.04 GiB (132.12 GB)"
      }
    ]
  },
  "numa_node":0
}

# ls -l /dev/dax*
crw-------. 1 root root 250, 1 Jul  9 10:39 /dev/dax0.0
```

Create a 50GiB 'devdax' namespace

```text
# ndctl create-namespace -s 50G
{
  "dev":"namespace0.0",
  "mode":"fsdax",
  "map":"dev",
  "size":"49.22 GiB (52.85 GB)",
  "uuid":"b49087fc-9786-48d1-899b-6166c7c586f9",
  "raw_uuid":"f441a441-77ad-4286-9585-ac9511d5381a",
  "sector_size":512,
  "blockdev":"pmem0",
  "numa_node":0
}
```

#### **Sector Mode Examples**

Create a sector mode namespace using all available capacity and a default 4K sector size

```text
# ndctl create-namespace -m sector
{
  "dev":"namespace0.0",
  "mode":"sector",
  "size":"124.88 GiB (134.09 GB)",
  "uuid":"08f4e273-bbdd-4d1d-85e8-cf1f847e1df7",
  "raw_uuid":"f3bd03ad-4225-4919-a370-bb6293180e4d",
  "sector_size":4096,
  "blockdev":"pmem0s",
  "numa_node":0
}

# ls -l /dev/pmem0
brw-rw----. 1 root disk 259, 0 Jul  9 10:47 /dev/pmem0s
```

#### RAW Mode Examples

Create a 'raw' mode namespace using all available capacity

```text
# ndctl create-namespace -m raw
{
  "dev":"namespace0.0",
  "mode":"raw",
  "size":"125.00 GiB (134.22 GB)",
  "uuid":"24c1d696-b197-4c89-96b2-f91b60f4f295",
  "sector_size":512,
  "blockdev":"pmem0",
  "numa_node":0
}

# ls -l /dev/pmem0
brw-rw----. 1 root disk 259, 0 Jul  9 10:52 /dev/pmem0
```

## Editing Namespace Properties

Changing properties of existing namespaces can be done online using the `ndctl create-namespace -fe <namespace> <option=value>` command. The `-e, --reconfigure` flag edits existing namespaces. Using the `-f` flag does not require that the namespace be manually disabled. The command may still fail if the namespace is currently being used. Supported options are listed above in the [Creating Namespaces](managing-namespaces.md#creating-namespaces) section, or review the ndctl-create-namespace [man page](man-pages.md). Some namespace properties are read-only and cannot be changed using the `ndctl` utility.

[Resizing Namespaces](managing-namespaces.md#resizing-namespaces) or [Changing Namespace Modes](managing-namespaces.md#changing-namespace-modes) can be achieved by changing existing namespace properties. They are discussed below under their own headings to provide additional detail.

#### Examples

Add or Change a namespace name \(also referred to as a tag or description\):

```text
# ndctl create-namespace -fe namespace0.0 -n "prod_web_db1"
{
  "dev":"namespace0.0",
  "mode":"fsdax",
  "map":"dev",
  "size":"49.22 GiB (52.85 GB)",
  "uuid":"086d4800-4dec-41f4-a3b8-bb3e53974c54",
  "raw_uuid":"411b37d2-284e-4626-8724-efd0855e3c39",
  "sector_size":512,
  "blockdev":"pmem0",
  "name":"prod_web_db1",
  "numa_node":0
}
```

## Resizing Namespaces

{% hint style="danger" %}
WARNING: Data will be lost or corrupted if the new size is smaller than the existing size or extending the namespace and growing the filesystem\(s\). Backup all data before resizing namespaces.
{% endhint %}

The following example resizes an existing 'fsdax' namespace \(namespace0.0\) to 20GiB:

```text
# ndctl create-namespace -fe namespace0.0 -s 20G
{
  "dev":"namespace0.0",
  "mode":"fsdax",
  "map":"dev",
  "size":"19.69 GiB (21.14 GB)",
  "uuid":"75cac06f-fc80-4c51-8f0b-2d73e2d2e31b",
  "raw_uuid":"89b14037-0299-4887-8acb-7c31be3ae31b",
  "sector_size":512,
  "blockdev":"pmem0",
  "name":"prod_web_db1",
  "numa_node":0
}
```

If there is an existing EXT4 or XFS filesystem on this device, the partition table may need to be manually changed to accommodate the new device capacity before the filesystem can be resized. Due to the many configuration options available when using fsdax devices, such as whether it's being managed by the Linux device-mapper, LVM \(Linux Volume Manager\), or just an XFS/EXT4 filesystem on top, refer to the relevant Linux distribution documentation for specific procedures to handle your configuration.

## Changing Namespace Modes

The namespace mode can be changed on any enabled or disabled namespace. Valid modes are raw, sector, fsdax, and devdax. Refer to the `ndctl-create-namespace` man page or [Namespaces](concepts/nvdimm-namespaces.md) chapter for more information on each mode.

{% hint style="warning" %}

* Changing the namespace mode will destroy all current data.  Backup all data before proceeding.
* If a filesystem using an fsdax or sector namespace is mounted, unmount it before changing the mode.
* If a devdax or raw namespace is currently being used by a running application, stop the application before changing modes.

Switching modes of an enabled/active namespace can be done using `ndctl create-namespace -fe <namespace> --mode=<new_mode>`. For example, to switch an enabled fsdax namespace to devdax mode, use:

```text
# ndctl list -N -n namespace0.0
{
  "dev":"namespace0.0",
  "mode":"fsdax",
  "map":"mem",
  "size":4294967296,
  "sector_size":512,
  "blockdev":"pmem0",
  "numa_node":0
}

# ndctl create-namespace -fe namespace0.0 --mode=devdax
{
  "dev":"namespace0.0",
  "mode":"devdax",
  "map":"dev",
  "size":"3.94 GiB (4.23 GB)",
  "uuid":"d71504d1-0286-474d-839a-52afe7d0da5f",
  "daxregion":{
    "id":0,
    "size":"3.94 GiB (4.23 GB)",
    "align":2097152,
    "devices":[
      {
        "chardev":"dax0.0",
        "size":"3.94 GiB (4.23 GB)"
      }
    ]
  },
  "numa_node":0
}

# ndctl list -N -n namespace0.0
{
  "dev":"namespace0.0",
  "mode":"devdax",
  "map":"dev",
  "size":4225761280,
  "uuid":"d71504d1-0286-474d-839a-52afe7d0da5f",
  "chardev":"dax0.0",
  "numa_node":0
}
```

If the namespace is currently disabled, the `-f` flag is not required.

## Destroying Namespaces

Namespaces should be inactive/disabled prior to deletion otherwise a warning message is displayed. The following shows an attempt to destroy an active namespace

```text
# ndctl destroy-namespace namespace0.0
  Error: namespace0.0 is active, specify --force for re-configuration
  error destroying namespaces: Device or resource busy
```

First disable the namespace, then destroy it

```text
# ndctl disable-namespace namespace0.0
disabled 1 namespace

# ndctl destroy-namespace namespace0.0
destroyed 1 namespace
```

Alternatively using the `-f` flag to the command forcibly destroys the namespace without doing any validation checks and does not require that the namespace be disabled first, eg:

```text
# ndctl destroy-namespace -f namespace0.0
destroyed 1 namespace
```

## Disabling Namespaces

{% hint style="warning" %}
**Warning:** Disabling namespaces while the namespace is mounted or in-use will result in undefined behavior by the application\(s\) using the namespace. Always stop the application and unmount filesystems \(fsdax mode\) before disabling namespaces.
{% endhint %}

1\) Disable the namespace \(namespace0.0\) using:

```text
# ndctl disable-namespace namespace0.0
disabled 1 namespace
```

2\) Verify the namespace is _disabled_. The `-i` flag is required to display inactive/disabled namespaces otherwise only active/enabled namespaces are shown. The example below filters the output to a specific namespace \(namespace0.0\) to avoid potentially large volumes of output being shown:

```text
# ndctl list -Ni -n namespace0.0
{
  "dev":"namespace0.0",
  "mode":"raw",
  "size":21474836480,
  "uuid":"89b14037-0299-4887-8acb-7c31be3ae31b",
  "sector_size":512,
  "state":"disabled",
  "name":"prod_web_db1",
  "numa_node":0
}
```

## Enabling Namespaces

1\) Enable the namespace \(namespace0.0\) using:

```text
# ndctl enable-namespace namespace0.0
enabled 1 namespace
```

2\) Verify the namespace is enabled. The state is only displayed for disabled namespaces. If the state field is not listed, the namespace is assumed to be enabled.

```text
# ndctl list -N -n namespace0.0
{
  "dev":"namespace0.0",
  "mode":"fsdax",
  "map":"dev",
  "size":21137195008,
  "uuid":"75cac06f-fc80-4c51-8f0b-2d73e2d2e31b",
  "raw_uuid":"89b14037-0299-4887-8acb-7c31be3ae31b",
  "sector_size":512,
  "blockdev":"pmem0",
  "name":"prod_web_db1",
  "numa_node":0
}
```

## Checking Sector Namespaces

The `check-namespace` command only works on namespaces in _sector mode_. Attempting to check namespaces in any other mode will yield an error similar to the following which attempts to check an fsdax mode namespace:

```text
# ndctl check-namespace namespace0.0
namespace0.0: namespace_check: namespace0.0: check aborted, namespace online
error checking namespaces: Device or resource busy
```

A namespace in the sector mode will have metadata on it to describe the Kernel BTT \(Block Translation Table\). The check-namespace command can be used to check the consistency of this metadata, and optionally, also attempt to repair it, if it has enough information to do so.

The command usage is:

```text
ndctl check-namespace <namespace> [<options>]
```

Where the available options include:

```text
    -b, --bus <bus-id>    limit namespace to a bus with an id or provider of <bus-id>
    -r, --region <region-id>
                          limit namespace to a region with an id or name of <region-id>
    -v, --verbose         emit extra debug messages to stderr
    -R, --repair          perform metadata repairs
    -L, --rewrite-log     regenerate the log
    -f, --force           check namespace even if currently active
```

**Examples**

The namespace being checked has to be disabled before initiating a check on it as a precautionary measure.

```text
# ndctl disable-namespace namespace0.0
# ndctl check-namespace namespace0.0
```

The `-f, --force` option can override the need to manually disable the namespace first, although it is not recommended to do so:

```text
# ndctl check-namespace -f namespace0.0
checked 1 namespace
```

Adding the `-v, --verbose` option yields more information, eg:

```text
# ndctl check-namespace -f namespace0.0 -v
namespace0.0: namespace_check: checking namespace0.0
namespace0.0: btt_discover_arenas: found 1 BTT arena
namespace0.0: log_set_indices: arena[0]: log_index_0 = 0
namespace0.0: log_set_indices: arena[0]: log_index_1 = 1
namespace0.0: btt_check_arenas: checking arena 0
checked 1 namespace
```

Check a namespace and perform repairs if possible or required can be done using:

```text
# ndctl disable-namespace namespace0.0
# ndctl check-namespace --repair namespace0.0
```

