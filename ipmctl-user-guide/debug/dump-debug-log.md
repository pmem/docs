# Dump Debug Log

Dumps encoded firmware debug logs from specified persistent memory modules and optionally decodes to human readable text.

```text
ipmctl dump [OPTIONS] -destination (file_prefix) [-dict (file)] -debug -dimm (DimmIDs) [PROPERTIES]
```

### **Targets**

* `-destination (file_prefix)`: The command will create files that use the given filename as a prefix and append the DIMM UID, DIMM handle, debug log source, and the appropriate file type \(.bin for encoded logs, .txt for decoded logs\) onto the end.

  > file\_prefix\_Uid\_Handle\_logsource.\[bin,txt\]

* `-dict (path)`: Optional file path to the dictionary file. If supplied, the command will create both the binary debug log and a text file with decoded log data with the file prefix specified by destination.
* `-dimm (DimmIDs)`: Dumps the debug logs from the specified modules.

### **Examples** 

Dump and decode the debug log from persistent memory modules 0x0001 and 0x0011 using the dictionary file.

```text
$ ipmctl dump -destination file_prefix -dict nlog_dict.txt -debug -dimm 0x0001,0x0011
```

### **Sample Output**

```text
Dumped media FW debug logs to file
(file_prefix_8089-A1-1816-00000016_0x0001_media.bin)
Decoded 456 records to file (file_prefix_8089-A1-1816-00000016_0x0001_media.txt)
No spi FW debug logs found
```

