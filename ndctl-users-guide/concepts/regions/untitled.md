# Regions, Atomic Sectors, and DAX

One of the few reasons to allow multiple BLK namespaces per REGION is so that each BLK-namespace can be configured with a Block Translation Table \(BTT\) with unique atomic sector sizes. While a PMEM device can host a BTT, the LABEL specification does not provide for a sector size to be specified for a PMEM namespace. This is due to the expectation that the primary usage model for PMEM is via DAX, and the BTT is incompatible with DAX. However, for the cases where an application or filesystem still needs atomic sector update guarantees, it can register a BTT on a PMEM device or partition.

## Example NVDIMM Platform

Figure 1 shows an example platform with four NVDIMMs, two integrated memory controllers \(IMC\), and a single CPU .

```text
                             (a)               (b)           DIMM   BLK-REGION
          +-------------------+--------+--------+--------+
+------+  |       pm0.0       | blk2.0 | pm1.0  | blk2.1 |    0      region2
| imc0 +--+- - - region0- - - +--------+        +--------+
+--+---+  |       pm0.0       | blk3.0 | pm1.0  | blk3.1 |    1      region3
   |      +-------------------+--------v        v--------+
+--+---+                               |                 |
| cpu0 |                                     region1
+--+---+                               |                 |
   |      +----------------------------^        ^--------+
+--+---+  |           blk4.0           | pm1.0  | blk4.0 |    2      region4
| imc1 +--+----------------------------|        +--------+
+------+  |           blk5.0           | pm1.0  | blk5.0 |    3      region5
          +----------------------------+--------+--------+
```

_Figure 1: Example sysfs layout_ 

Each unique interface \(BLK or PMEM\) to DPA space is identified by a region device with a dynamically assigned id \(REGION0 - REGION5\).

1. The first portion of DIMM0 and DIMM1 are interleaved as REGION0. A

    single PMEM namespace is created in the REGION0-SPA-range that spans most

    of DIMM0 and DIMM1 with a user-specified name of "pm0.0". Some of that

    interleaved system-physical-address range is reclaimed as BLK-aperture

    accessed space starting at DPA-offset \(a\) into each DIMM.  In that

    reclaimed space we create two BLK-aperture "namespaces" from REGION2 and

    REGION3 where "blk2.0" and "blk3.0" are just human readable names that

    could be set to any user-desired name in the LABEL.

2. In the last portion of DIMM0 and DIMM1 we have an interleaved

   ```text
   system-physical-address range, REGION1, that spans those two DIMMs as
   ```

    well as DIMM2 and DIMM3.  Some of REGION1 is allocated to a PMEM namespace

    named "pm1.0", the rest is reclaimed in 4 BLK-aperture namespaces \(for

    each DIMM in the interleave set\), "blk2.1", "blk3.1", "blk4.0", and

    "blk5.0".

3. The portion of DIMM2 and DIMM3 that do not participate in the REGION1

    interleaved system-physical-address range \(i.e. the DPA address past

    offset \(b\) are also included in the "blk4.0" and "blk5.0" namespaces.

     Note, that this example shows that BLK-aperture namespaces don't need to

    be contiguous in DPA-space.

     This bus is provided by the kernel under the device

    `/sys/devices/platform/nfit_test.0` when CONFIG\_NFIT\_TEST is enabled and

    the nfit\_test.ko module is loaded.  This not only test LIBNVDIMM but the

    acpi\_nfit.ko driver as well.

