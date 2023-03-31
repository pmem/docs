# Show Error Log

Shows thermal or media errors on the specified persistent memory modules.

```
ipmctl show [OPTIONS] -error (Thermal|Media) [TARGETS] [PROPERTIES]
```

## **Targets**

* `-dimm (DimmIDs)`: Show only the events on specific modules by supplying the DIMM target and one or more comma-separated DimmIDs.

## **Properties**

* `SequenceNumber`: Error log entries are stored with a sequence number starting with 1 and rolling over back to 1 after 65535. Limit the error log entries returned by providing a sequence number. Only errors with a sequence number equal to or higher than that provided will be returned. The default is 1.
* `Level`: Severity level of errors to be fetched. One of:
  * "High": high severity errors (default)
  * "Low": Low severity errors
* `Count`: Max number of error entries to be fetched and printed. The default is 8 for media errors and 16 for thermal errors.

## **Examples**

Show all high thermal error log entries

```
$ sudo ipmctl show -error Thermal Level=High
```

Show all low media error log entries

```
$ sudo ipmctl show -error Thermal Level=Low
```

## **Sample Output**

```
Thermal Error occurred on Dimm (DimmID):
System Timestamp : 1527273299
Temperature : 88C
Reported : 4 - Critical
Temperature Type : 0 - Media Temperature
Sequence Number : 1
```

```
Media Error occurred on Dimm (DimmID):
System Timestamp : 1527266471
DPA : 0x000014c0
PDA : 0x00000600
Range : 4B
Error Type : 4 - Locked/Illegal Access
Error Flags : DPA Valid
Transaction Type : 11 - CSR Write
Sequence Number : 2
```
