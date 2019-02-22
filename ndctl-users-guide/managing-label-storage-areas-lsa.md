# Managing Label Storage Areas \(LSA\)

The namespace label area is a small persistent partition of capacity available on some NVDIMM devices. The label area is used to store the definition of any NVDIMM namespaces.

The labels on the NVDIMMs cannot be edited directly. Changing the configuration can be achieved using the ndctl **create** or **destroy** commands. See [Managing Namespaces](managing-namespaces.md) and [Managing Regions](managing-regions.md). `ndctl` provides several label related commands shown below:

* [check-labels](managing-label-storage-areas-lsa.md#checking-labels) - determine if the given dimm\(s\) have a valid namespace index block
* [init-labels](managing-label-storage-areas-lsa.md#initializing-labels) - initialize the label data area on a dimm or set of dimms
* [read-labels](managing-label-storage-areas-lsa.md#reading-labels) - read out the label area on a dimm or set of dimms
* [write-labels](managing-label-storage-areas-lsa.md#writing-labels) - write data to the label area on a dimm
* [zero-labels](managing-label-storage-areas-lsa.md#erasing-zeroing-labels) - zero out the label area on a dimm or set of dimms

## Checking Labels

The `check-labels` command validates the index block within the label storage area on the specified NVDIMMs. If issues are found, running the command in verbose mode using the `-v` option, reports the reason the index block is deemed invalid.

```text
usage: ndctl check-labels <nmem0> [<nmem1>..<nmemN>] [<options>]

    -b, --bus <bus-id>    <nmem> must be on a bus with an id/provider of <bus-id>
    -v, --verbose         turn on debug
```

1\) Identify the NVDIMMs \(nmem's\) in the host using the `ndctl list -D` command:

```text
# ndctl list -D
[
  {
    "dev":"nmem3",
    "id":"8089-a1-1811-0000005d",
    "handle":4353,
    "phys_id":68
  },
  {
    "dev":"nmem0",
    "id":"8089-a1-1811-00000058",
    "handle":1,
    "phys_id":32
  },
  {
    "dev":"nmem2",
    "id":"8089-a1-1811-00000068",
    "handle":4097,
    "phys_id":56
  }
]
```

2\) Check the labels of one or more devices.

To check the labels on a single device:

```text
# ndctl check-labels nmem3
```

To check the labels on multiple devices:

```text
# ndctl check-labels nmem3 nmem0 nmem2
```

To check the labels on all devices:

```text
# ndctl check-labels all
```

To display more information, if available, use the `-v` option:

```text
# ndctl check-labels nmem3 -v
```

{% hint style="info" %}
Note: The `-v` option may not produce any additional information if the labels are valid or ndctl was not built with logging or debug options.
{% endhint %}

## Initializing Labels

This command can be used to initialize the namespace index block if it is missing or reinitialize it if it is damaged or was previously zero'd. Note that reinitialization effectively destroys all existing namespace labels on the DIMM.

{% hint style="danger" %}
This is an expert mode operation. Overwriting labels will cause data loss. Backup all data prior to overwriting labels.
{% endhint %}

```text
 usage: ndctl init-labels <nmem0> [<nmem1>..<nmemN>] [<options>]

    -b, --bus <bus-id>    <nmem> must be on a bus with an id/provider of <bus-id>
    -v, --verbose         turn on debug
    -f, --force           force initialization even if existing index-block present
    -V, --label-version <version-number>
                          namespace label specification version (default: 1.1)
```

Before initializing the labels, all namespaces and regions must be destroyed or disabled. A warning message is displayed when attempting to initialize labels on active regions.

```text
# ndctl init-labels nmem3 nmem2
nmem3: regions active, abort label write
nmem2: regions active, abort label write
```

1\) Identify the active/enabled NVDIMM devices, Regions, and Namespaces

```text
# ndctl list -DRN
{
  "dimms":[
    {
      "dev":"nmem3",
      "id":"8089-a1-1811-0000005d",
      "handle":4353,
      "phys_id":68
    },
    {
      "dev":"nmem0",
      "id":"8089-a1-1811-00000058",
      "handle":1,
      "phys_id":32
    },
    {
      "dev":"nmem2",
      "id":"8089-a1-1811-00000068",
      "handle":4097,
      "phys_id":56
    }
  ],
  "regions":[
    {
      "dev":"region1",
      "size":534723428352,
      "available_size":0,
      "type":"pmem",
      "iset_id":3026489321642266624,
      "mappings":[
        {
          "dimm":"nmem3",
          "offset":268435456,
          "length":267361714176,
          "position":1
        },
        {
          "dimm":"nmem2",
          "offset":268435456,
          "length":267361714176,
          "position":0
        }
      ],
      "persistence_domain":"memory_controller",
      "namespaces":[
        {
          "dev":"namespace1.0",
          "mode":"fsdax",
          "map":"dev",
          "size":526366277632,
          "uuid":"76fc4dd1-e793-4eee-81ed-2f31275eb820",
          "blockdev":"pmem1"
        }
      ]
    },
    {
      "dev":"region0",
      "size":267361714176,
      "available_size":0,
      "max_available_extent":0,
      "type":"pmem",
      "mappings":[
        {
          "dimm":"nmem0",
          "offset":268435456,
          "length":267361714176,
          "position":0
        }
      ],
      "persistence_domain":"memory_controller"
    }
  ]
}
```

2\) Destroy or disable the namespace\(s\) belonging to the NVDIMMs \(nmem's\) that require re-initializing.

```text
# ndctl destroy-namespace -f namespace1.0
- or -
# ndctl disable-namespace namespace1.0
# ndctl destroy-namespace namespace1.0
```

3\) Disable the region\(s\) belonging to the NVDIMMs \(nmem's\) that require re-initializing.

```text
# ndctl disable-region region1
```

4\) Initialize the labels

```text
# ndctl init-labels nmem2 nmem3
```

If the `init-labels` command returns "error: labels already initialized", use the `-f`, `--force` option.

5\) Enable the region\(s\). After initializing the labels, the region\(s\) may not be brought online automatically. List the active and disabled regions:

```text
# ndctl list -iR
[
  {
    "dev":"region1",
    "size":534723428352,
    "available_size":534723428352,
    "type":"pmem",
    "iset_id":3026489321642266624,
    "state":"disabled",
    "persistence_domain":"memory_controller"
  },
  {
    "dev":"region0",
    "size":267361714176,
    "available_size":0,
    "max_available_extent":0,
    "type":"pmem",
    "persistence_domain":"memory_controller"
  }
]
```

Activate any disabled regions

```text
# ndctl enable-region region1

# ndctl list -iR
[
  {
    "dev":"region1",
    "size":534723428352,
    "available_size":534723428352,
    "type":"pmem",
    "iset_id":3026489321642266624,
    "persistence_domain":"memory_controller"
  },
  {
    "dev":"region0",
    "size":267361714176,
    "available_size":0,
    "max_available_extent":0,
    "type":"pmem",
    "persistence_domain":"memory_controller"
  }
]
```

6\) New namespaces can now be created on the regions. See [Managing Namespaces](managing-namespaces.md) for more information.

## Reading Labels

The `read-labels` command dumps the data in a dimm’s label area to stdout or a file in raw or JSON format. When multiple dimms \(nmem's\) are specified, the data is concatenated.

Usage:

```text
       ndctl read-labels <nmem0> [<nmem1>..<nmemN>] [<options>]

OPTIONS
       <memory device(s)>
           One or more nmemX device names. The keyword all can be specified to operate on every dimm in the system, optionally
           filtered by bus id (see --bus= option).

       -b, --bus=
           Limit operation to memory devices (dimms) that are on the given bus. Where bus can be a provider name or a bus id
           number.

       -v
           Turn on verbose debug messages in the library (if ndctl was built with logging and debug enabled).

       -o, --output
           output file

       -j, --json
           parse the label data into json assuming the NVDIMM Namespace Specification format.
```

The following shows a label from nvdimm0 \(nmem0\) printed to stdout in JSON format.

```text
# ndctl read-labels -j nmem0
{
  "dev":"nmem0",
  "index":[
    {
      "signature":"NAMESPACE_INDEX",
      "major":1,
      "minor":2,
      "labelsize":256,
      "seq":2,
      "nslot":510
    },
    {
      "signature":"NAMESPACE_INDEX",
      "major":1,
      "minor":2,
      "labelsize":256,
      "seq":1,
      "nslot":510
    }
  ],
  "label":[
    {
      "uuid":"9ed56df2-c0a9-4e17-b82d-28f642f95a22",
      "name":"",
      "slot":0,
      "position":0,
      "nlabel":1,
      "isetcookie":710683120631319073,
      "lbasize":512,
      "dpa":268435456,
      "rawsize":267361714176,
      "type_guid":"79d3f066-f3b4-7440-ac43-0d3318b78cdb",
      "abstraction_guid":"ba006426-9ffb-7746-bcb0-968f11d0d225"
    }
  ]
}
read 1 nmem
```

To backup an NVDIMM label using the raw format, use:

```text
# ndctl read-labels nmem0 > nmem0.lsa
```

## Writing Labels

{% hint style="danger" %}
This is an expert mode operation. Overwriting labels will cause data loss. Backup all data prior to overwriting labels.
{% endhint %}

Writing labels overwrites the current label with the contents of the specified file, or stdin, collected from a [read-labels](managing-label-storage-areas-lsa.md#reading-labels) operation from a different label, or a previous label backup.

Read data from the input filename, or stdin, and write it to the given device. The device being written to must not be active in any region, otherwise the kernel will not allow write access to the device’s label data area.

The following shows how a raw label from an active nvdimm can be written to an inactive nvdimm.

```text
$ ndctl read-labels nmem0 -o nmem0.label.backup.raw
$ ndctl write-labels nmem1 -i nmem0.label.backup.raw
```

The above read-label raw output can be piped directly to the write-label command using:

```text
$ ndctl read-labels nmem0 | ndctl write-labels nmem1
```

## Erasing/Zeroing Labels

The `zero-labels` command resets the device to its default state by deleting all labels.

{% hint style="danger" %}
This operation cannot be undone unless a previous backup of the labels was created using the [read-labels](managing-label-storage-areas-lsa.md#reading-labels) command.

All data on the device will be destroyed. Create a backup before proceeding.
{% endhint %}

Before erasing/zeroing the labels, all namespaces and regions must be destroyed or disabled.

1\) Identify the active/enabled NVDIMM devices, Regions, and Namespaces

```text
# ndctl list -DRN
{
  "dimms":[
    {
      "dev":"nmem3",
      "id":"8089-a1-1811-0000005d",
      "handle":4353,
      "phys_id":68
    },
    {
      "dev":"nmem0",
      "id":"8089-a1-1811-00000058",
      "handle":1,
      "phys_id":32
    },
    {
      "dev":"nmem2",
      "id":"8089-a1-1811-00000068",
      "handle":4097,
      "phys_id":56
    }
  ],
  "regions":[
    {
      "dev":"region1",
      "size":534723428352,
      "available_size":0,
      "type":"pmem",
      "iset_id":3026489321642266624,
      "mappings":[
        {
          "dimm":"nmem3",
          "offset":268435456,
          "length":267361714176,
          "position":1
        },
        {
          "dimm":"nmem2",
          "offset":268435456,
          "length":267361714176,
          "position":0
        }
      ],
      "persistence_domain":"memory_controller",
      "namespaces":[
        {
          "dev":"namespace1.0",
          "mode":"fsdax",
          "map":"dev",
          "size":526366277632,
          "uuid":"76fc4dd1-e793-4eee-81ed-2f31275eb820",
          "blockdev":"pmem1"
        }
      ]
    },
    {
      "dev":"region0",
      "size":267361714176,
      "available_size":0,
      "max_available_extent":0,
      "type":"pmem",
      "mappings":[
        {
          "dimm":"nmem0",
          "offset":268435456,
          "length":267361714176,
          "position":0
        }
      ],
      "persistence_domain":"memory_controller"
    }
  ]
}
```

2\) Destroy or disable the namespace\(s\) belonging to the NVDIMMs \(nmem's\) that require re-initializing.

```text
# ndctl destroy-namespace -f namespace1.0
- or -
# ndctl disable-namespace namespace1.0
# ndctl destroy-namespace namespace1.0
```

3\) Disable the region\(s\) belonging to the NVDIMMs \(nmem's\) that require re-initializing.

```text
# ndctl disable-region region1
```

4\) To erase the labels on a single 'nmem0' device.

```text
$ ndctl zero-labels nmem0
```

To erase labels on multiple devices, use:

```text
$ ndctl zero-labels nmem0 nmem2 nmem3
```

5\) Enable the region\(s\). After initializing the labels, the region\(s\) may not be brought online automatically. List the active and disabled regions:

```text
# ndctl list -iR
[
  {
    "dev":"region1",
    "size":534723428352,
    "available_size":534723428352,
    "type":"pmem",
    "iset_id":3026489321642266624,
    "state":"disabled",
    "persistence_domain":"memory_controller"
  },
  {
    "dev":"region0",
    "size":267361714176,
    "available_size":0,
    "max_available_extent":0,
    "type":"pmem",
    "persistence_domain":"memory_controller"
  }
]
```

Activate any 'disabled' regions

```text
# ndctl enable-region region1

# ndctl list -iR
[
  {
    "dev":"region1",
    "size":534723428352,
    "available_size":534723428352,
    "type":"pmem",
    "iset_id":3026489321642266624,
    "persistence_domain":"memory_controller"
  },
  {
    "dev":"region0",
    "size":267361714176,
    "available_size":0,
    "max_available_extent":0,
    "type":"pmem",
    "persistence_domain":"memory_controller"
  }
]
```

6\) New namespaces can now be created on the regions. See [Managing Namespaces](managing-namespaces.md) for more information.

