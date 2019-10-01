# Show Device Performance

Shows performance metrics for one or more persistent memory modules.

```text
ipmctl show [OPTIONS] -performance [METRICS] [TARGETS]
```

### **Metrics** 

Shows output of a specific performance metric by supplying the metric name. See RETURN DATA for more information. One of:

* `MediaReads`: Number of 64 byte reads from media on the module since last AC cycle.
* `MediaWrites`: Number of 64 byte writes to media on the module since last AC cycle.
* `ReadRequests`: Number of DDRT read transactions the module has serviced since last AC cycle.
* `WriteRequests`: Number of DDRT write transactions the module has serviced since last AC cycle.
* `TotalMediaReads`: Number of 64 byte reads from media on the module over its lifetime.
* `TotalMediaWrites`: Number of 64 byte writes to media on the module over its lifetime.
* `TotalReadRequests`: Number of DDRT read transactions the module has serviced over its lifetime.
* `TotalWriteRequests`: Number of DDRT write transactions the module has serviced over its lifetime.

The default is to display all performance metrics.

### **Targets**

* `dimm (DimmIDs)`: Show the performance metrics of specific modules by supplying one or more comma-separated DimmIDs. The default is to display sensors for all manageable modules.

### **Examples**

Show all performance metrics for all modules in the server

```text
$ ipmctl show -dimm -performance
```

Show the number of 64 byte reads since last AC cycle for all modules in the server

```text
$ ipmctl show -dimm -performance MediaReads
```

