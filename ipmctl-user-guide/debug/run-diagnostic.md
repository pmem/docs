# Run Diagnostic

Runs a diagnostic test.

```text
ipmctl start [OPTIONS] -diagnostic (Quick|Config|Security|FW) -dimm(DIMMIDs)
```

### **Targets**

* `-diagnostic (Quick|Config|Security|FW)`: Run a specific test by supplying its name. By default all tests are run. One of:
  * `Quick` - This test verifies that the persistent memory module host mailbox is accessible and that basic health indicators can be read and are currently reporting acceptable values.
  * `Config` - This test verifies that the BIOS platform configuration matches the installed hardware and the platform configuration conforms to best known practices.
  * `Security` - This test verifies that all modules have a consistent security state. It’s a best practice to enable security on all modules rather than just some.
  * `FW` - This test verifies that all modules of a given model have consistent firmware installed and other firmware modifiable attributes are set in accordance with best practices.
    * Note that the test does not have a means of verifying that the installed firmware is the optimal version for a given Intel Optane DC memory module model - just that it’s been consistently applied across the system.
* `dimm (DimmIDs)`: Runs a diagnostic test on specific modules by supplying one or more comma-separated DimmIDs. The default is to run the specified tests on all manageable modules. Only valid for the `Quick` diagnostic test.

### **Examples** 

Run all diagnostics

```text
$ ipmctl start -diagnostic
```

Run the quick check diagnostic on module 0x0001

```text
$ ipmctl start -diagnostic Quick -dimm 0x0001
```

### **Return Data**

Each diagnostic generates one or more log messages. A successful test generates a single log message per module indicating that no errors were found. A failed test might generate multiple log messages each highlighting a specific error with all the relevant details. Each log contains the following information:

```text
`TestName`: The test name. One of:
- `Quick`
- `Config`
- `Security`
- `FW`

`State`: The severity of the error. One of:
- `Ok`
- `Warning`
- `Failed`
- `Aborted`

`Message`: A free form textual description of the error.
```

