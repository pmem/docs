# Provisioning

Intel Optane persistent memory modules can be provisioned into three different operating modes:

* **App Direct**: Persistent memory exposed as block devices to the operating system and DRAM is used as main memory.
* **Memory Mode**: Uses persistent memory as main memory and DRAM is used as a cache not explicitly managed by the operating system. Data placement is managed by the memory controller.&#x20;
* **Mixed Mode**: A combination of App Direct and Memory Modes where a percentage of total memory is allocated for Memory Mode, and the rest is used for App Direct.

You can learn more about each operating mode and which is right for your application in this [video](https://www.youtube.com/watch?v=gqo3gty-R4s).

To learn more about how ipmctl works with the hardware see the [Intel® Optane™ Persistent Memory OS Provisioning Specification](https://cdrdv2.intel.com/v1/dl/getContent/634430), which describes all the firmware interface commands used for this operation.

{% hint style="warning" %}
**WARNING:** Provisioning or changing modes may result in data loss. Data should be backed up to other storage before executing this command.

Changing a memory allocation goal modifies how the platform firmware maps persistent memory in the system address space which may result in data loss or inaccessible data, but does not explicitly delete or modify user data found in persistent memory.
{% endhint %}
