# Managing NVDIMMs

Managing physical or emulated NVDIMMs using `ndctl` has no functional difference. Physical NVDIMM features and options may be controlled through the system BIOS. The BIOS cannot see emulated NVDIMMs.

Observe the following restrictions when managing NVDIMMs

* DO NOT change the memory slot of physical NVDIMMs when they are part of an Interleave Set.  Doing so changes the order of interleaving so data access will be compromised or corrupted.  If interleaving is disabled, moving NVDIMMs to different slot locations is okay, but not recommended.
* DO NOT disable NVDIMMs when they are part of an active Region and/or Namespace as this will prevent data access and may corrupt data.
* ALWAYS backup the data and make a copy of the configuration layout prior to any changes.

## Listing active/enabled NVDIMMs

The `ndct list -D` , or equivalent `ndct list --dimm` , can be used to show active/enabled NVDIMM devices on the system, eg:

```text
# ndctl list -D
{
  "dev":"nmem0",
  "id":"8089-a2-1809-00000107",
  "handle":1,
  "phys_id":29
}
```

## Listing disabled/inactive NVDIMMs

By default, `ndctl` only lists enabled/active dimms, regions, and namespaces. To include previously disabled \(inactive\) NVDIMMs, include the `-i` flag to show both enabled and disabled devices, eg:

```text
# ndctl list -Di
{
  "dev":"nmem0",
  "id":"8089-a2-1809-00000107",
  "handle":0,
  "phys_id":29
}
{
  "dev":"nmem1",
  "id":"9759-b5-1459-00000502",
  "handle":1,
  "phys_id":30
  "state":"disabled"
}
...
```

{% hint style="info" %}
NVDIMM vendor specific tools can be used to display more information about the NVDIMMs from the operating system layer. For example, Intel Optane DC Persistent Memory Modules can be managed using the [ipmctl](https://github.com/intel/ipmctl) utility. These tools are outside the scope for this documentation. Refer to the vendor specific documentation.
{% endhint %}

## Disabling NVDIMMs

NVDIMMs can only be disabled if they have no active Regions or Namespaces. If an active/enabled namespace and/or region exists, a message is displayed:

```text
# ndctl disable-dimm nmem0
nmem0 is active, skipping...
disabled 0 nmem
```

1\) List the current active/enabled configuration

```text
# ndctl list -NRD
```

2\) Verify no fsdax or devdax namespaces are mounted or in-use by running applications

3\) Destroy or disable any active/enabled namespace\(s\).

```text
# ndctl disable-namespace <namespaceX.Y>
- or -
# ndctl destroy-namespace <namespaceX.Y>
```

See [Destroying Namespaces](managing-namespaces.md#destroying-namespaces) or [Disabling Namespaces](managing-namespaces.md#disabling-namespaces) for more information.

4\) Disable the regions used by the NVDIMM \(nmem\) that needs to be disabled

```text
# ndctl disable-region <region.X>
```

See [Disabling Regions](managing-regions.md#disabling-regions) for more information.

5\) Disable a single, subset, or all NVDIMM \(nmem\) devices

To disable a single NVDIMM, use:

```text
# ndctl disable-dimm nmem0
disabled 1 nmem
```

To disable a subset or specific list or NVDIMMs, use:

```text
# ndctl disable-dimm nmem0 nmem1 nmem5
disabled 3 nmem
```

To disable all NVDIMMs, use:

```text
# ndctl disable-dimm all
disabled 12 nmem
```

6\) Verify the NVDIMM\(s\) are disabled by listing inactive dimms and verifying the 'state':

```text
# ndctl list -Di
{
  "dev":"nmem0",
  "id":"8089-a2-1809-00000107",
  "handle":1,
  "phys_id":29,
  "state":"disabled"
}
...
```

## Enabling NVDIMMs

1\) Verify the nmem device, or list of nmem devices, that need to be enabled using the `ndctl list -Di` command:

```text
# ndctl list -Di
{
  "dev":"nmem0",
  "id":"8089-a2-1809-00000107",
  "handle":1,
  "phys_id":29,
  "state":"disabled"
}
```

2\) Enable the NVDIMM\(s\)

To enable a single NVDIMM \(nmemX\) device, use:

```text
# ndctl enable-dimm nmem0
enabled 1 nmem
```

To enable a subset of disabled NVDIMMs, use:

```text
# ndctl enable-dimm nmem0 nmem1 nmem5
enabled 3 nmem
```

To enable all disabled NVDIMMs, use:

```text
# ndctl enable-dimm all
enabled 12 nmem
```

3\) Verify the state by listing all NVDIMMs

```text
# ndctl list -Di
```

A filtered list of NVDIMMs can shown using the `-d <nmemX>` or `-dimm <nmemX>` option, eg:

```text
# ndctl list -Di -d nmem0
- or -
# ndctl list -Di -dimm nmem0
```

