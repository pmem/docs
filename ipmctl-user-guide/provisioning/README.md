# Provisioning

Intel Optane DC persistent memory modules can be provisioned into three different operating modes:

* **App Direct**: Persistent memory exposed as block devices to operating system and DRAM is used as main memory.
* **Memory Mode**: Uses persistent memory as main memory and DRAM is used as a cache not explicitly managed by the operating system. Data placement is managed by the memory controller. 
* **Mixed Mode**: A combination of App Direct and Memory Modes where a percentage of total memory is allocated for Memory Mode, and the rest is used for App Direct.

You can learn more about each operating mode and which is right for your application in this [video](https://www.youtube.com/watch?v=gqo3gty-R4s).

> **WARNING**: Provisioning or changing modes may result in data loss. Data should be backed up to other storage before executing this command.
>
> > Changing a memory allocation goal modifies how the platform firmware maps persistent memory in the system address space \(SPA\) which may result in data loss or inaccessible data, but does not explicitly delete or modify user data found in persistent memory.

