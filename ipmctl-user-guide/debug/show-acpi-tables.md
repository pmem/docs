# Show ACPI Tables

Shows the system ACPI tables related to persistent memory modules.

```
ipmctl show [OPTIONS] -system (NFIT|PCAT|PMTT)
```

## **Targets**

* `system (NFIT|PCAT|PMTT)`: The system ACPI table(s) to display. By default both the NFIT and PCAT tables are displayed. One of:
  * "NFIT": The NVDIMM Firmware Interface Table
  * "PCAT": The Platform Capabilities Table
  *   "PMTT": The Platform Memory Topology Table

      Refer to the ACPI specification for detailed information about the ACPI tables.

## **Examples**

Show the ACPI NFIT

```
$ sudo ipmctl show -system NFIT
```

## **Return Data**

Returns the formatted data from the requested ACPI tables and their sub-tables. Refer to the ACPI specification for detailed information about the format of the ACPI tables.

> Note: All data is presented in ACPI little endian format.
