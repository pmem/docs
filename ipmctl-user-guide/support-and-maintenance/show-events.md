# Show Events

Shows persistent memory module related events. The options, targets, and properties can be used to filter the events. If no filters are provided, the default is to display up to 50 events. Refer to the Event Log Specification for detailed information about events.

```text
$ ipmctl show [OPTIONS] -event [TARGETS] [PROPERTIES]
```

### **Targets**

* `-dimm [(DimmID)]`: Filter output to events on a specific module by optionally supplying the DIMM target and one or more DimmID.

### **Properties**

* `Category`: Filters output to events of a specific category. One of:
  * `Diag`: Filters output to diagnostic events.
  * `FW`: Filters output to FW consistency diagnostic test events.
  * `Config`: Filters output to platform config diagnostic test events.
  * `PM`: Filters output to PM meta data diagnostic test events.
  * `Quick`: Filters output to quick diagnostic test events.
  * `Security`: Filters output to security diagnostic test events.
  * `Health`: Filters output to device health events.
  * `Mgmt`: Filters output to management software generated events.
* `Severity`: Filters output of events based on the severity of the event. One of:
  * `Info`: \(Default\) Shows informational, warning, and error severity events.
  * `Warning`: Shows warning and error events.
  * `Error`: Shows error events.
* `ActionRequired`: Filters output to events that require corrective action or acknowledgment.
  * `0`: Filters output to only show non-ActionRequired events.
  * `1`: Filters output to only show ActionRequired events.
* `Count`: Filters output of events limited to the specified count, starting with the most recent. Count may be a value from 1 to 2,147,483,647. Default = 50.

### **Examples** 

Display the 50 most recent events.

```text
$ ipmctl show -event
```

Show the 10 most recent error events. With the exception that this call limits the output to 10, this is equivalent to calling `ipmctl show -error`.

```text
$ ipmctl show -event count=10 severity=error
```

### **Return Data** 

This command displays a table with a row for each event matching the provided filters, showing most recent first.

* `Time`: Time of the event in the format MM:dd:yyyy:hh:mm:ss.
* `EventID`: The event identifier
* `Severity`: The severity of the event
* `ActionRequired` A flag indicating that the event requires corrective action or acknowledgment. One of:
  * `0`: A action is not required.
  * `1`: An action is required.
* `Code`: The event code defined by the SW Event Log specification
* `Message`: The event message

