# Managing Regions

A region is a grouping of one or more NVDIMMs, or an interleaved set, that can be divided up into one or more Namespaces. Regions are of type PMEM or BLK. See [PMEM or BLK](concepts/libnvdimm-pmem-and-blk-modes.md) modes for more information. The type can only be changed using the vendor specific NVDIMM utility that manages the NVDIMMs and/or a BIOS option if available.

## Disabling Regions

1\) List all active/enabled regions:

```text
# ndctl list -R
{
  "dev":"region0",
  "size":134217728000,
  "available_size":134217728000,
  "type":"pmem",
  "numa_node":0,
  "iset_id":-7501067817058727390,
  "state":"disabled",
  "persistence_domain":"memory_controller"
}
```

A filtered list of active/enabled regions can be displayed using the `-r <region-id>` or `--region <region-id>` option, eg:

```text
# ndctl list -R -r region0
- or -
# ndctl list -R -region region0
```

2\) Disable the region

```text
# ndctl disable-region region0
disabled 1 region
```

3\) Verify the region is disabled by including the `-i` option:

```text
# ndctl list -Ri
{
  "dev":"region0",
  "size":134217728000,
  "available_size":134217728000,
  "type":"pmem",
  "numa_node":0,
  "iset_id":-7501067817058727390,
  "state":"disabled",
  "persistence_domain":"memory_controller"
}
```

A filtered list of disabled/inactive regions can be displayed using the `-r <region-id>` or `--region <region-id>` option, eg:

```text
# ndctl list -Ri -r region0
- or -
# ndctl list -Ri -region region0
```

## Enabling Regions

1\) List disabled/inactive regions

```text
# ndctl list -Ri
{
  "dev":"region0",
  "size":134217728000,
  "available_size":134217728000,
  "type":"pmem",
  "numa_node":0,
  "iset_id":-7501067817058727390,
  "state":"disabled",
  "persistence_domain":"memory_controller"
}
```

A filtered list of active/enabled regions can be displayed using the `-r <region-id>` or `--region <region-id>` option, eg:

```text
# ndctl list -R -r region0
- or -
# ndctl list -R -region region0
```

2\) Enable the region

```text
# ndctl enable-region region0
enabled 1 region
```

3\) Verify the region is enabled using `ndctl list -R`. Note the 'state' field is not displayed for enabled regions.

```text
# ndctl list -R
{
  "dev":"region0",
  "size":134217728000,
  "available_size":134217728000,
  "type":"pmem",
  "numa_node":0,
  "iset_id":-7501067817058727390,
  "persistence_domain":"memory_controller"
}
```

