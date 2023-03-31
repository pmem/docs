# Concepts

To provision or change modes, create a **goal configuration** that will take effect after a system reboot. Goals specify which operating mode the modules are to be used in. The goal is stored on the persistent memory modules for the BIOS to read on the next reboot.

A **region** is a grouping of one or more persistent memory modules. Regions can be created as either non-interleaved, meaning one region per persistent memory module, or interleaved, which creates one large region over all modules in a CPU socket. Regions cannot be created across CPU sockets.

![Non-interleaved](https://user-images.githubusercontent.com/21182867/59884137-2a7b8580-936c-11e9-8f6a-e16aa3efcb19.png) ![Fully interleaved](https://user-images.githubusercontent.com/21182867/59884182-5991f700-936c-11e9-88dc-5f483f1d433a.png)

Many users choose to have one fully-interleaved set across their memory modules because this allows for increased bandwidth performance. Regions can be created or modified using the `ipmctl -create -goal` command, or via an option in the BIOS.

Regions can be divided up into one or more **namespaces**. A namespace defines a contiguously addressed range of non-volatile memory conceptually similar to a disk partition, or an NVM Express namespace. A namespace is the unit of persistent memory storage that appears in the /dev directory as a device that can be used for input and output or partitioned further. Intel recommends using the [ndctl utility](https://docs.pmem.io/ndctl-users-guide) for creating namespaces in Linux and the native PowerShell commands in Microsoft Windows. Namespaces can further be partitioned using commands such as fdisk and parted on Linux or native Microsoft Windows commands and utilities.

![Namespaces](https://user-images.githubusercontent.com/21182867/59884230-94942a80-936c-11e9-8b66-ab911a240fb0.png)

A **label** is similar to a partition table.  Intel persistent memory modules support labels that allow regions to be further divided into namespaces. A label contains metadata stored on a persistent memory module.
