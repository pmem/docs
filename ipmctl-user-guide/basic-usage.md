# Basic Usage

The `ipmctl` utility has many options. A complete list of commands can be shown by executing `ipmctl` with no arguments, `ipmctl help`, or reading the `ipmctl(1)` man page. Running `ipmctl`requires root privileges. `ipmctl` can also be used from UEFI. Namespaces are created using `ipmctl` at UEFI level or the [ndctl utility](../getting-started-guide/what-is-ndctl.md).

Usage:

```text
ipmctl COMMAND [OPTIONS] [TARGETS] [PROPERTIES]
```

Items in square brackets `[..]` are optional. Options, targets, and property values are separated by a pipe `|` meaning "or", and the default value is italicized. Items in parenthesis `(..)` indicate a user supplied value.

`ipmctl` commands include:

* help
* load
* set
* delete
* show
* create
* dump
* start

More information can be shown for each command using the `-verbose` flag, which is helpful for debugging.

