# Using the memmap Kernel Option

The `pmem` driver allows users to begin developing software using Direct Access Filesystems \(DAX\) such as EXT4 and XFS. A new `memmap` option was added that supports reserving one or more ranged of unassigned memory for use with emulated persistent memory. The `memmap` parameter documentation can be found at [https://www.kernel.org/doc/Documentation/admin-guide/kernel-parameters.txt](https://www.kernel.org/doc/Documentation/admin-guide/kernel-parameters.txt). This feature was upstreamed in the v4.0 Kernel. Kernel v4.15 introduced performance improvements and is recommended for production environments.

## Quick Start

The memmap option uses a`memmap=nn[KMG]!ss[KMG]` format; where`nn` is the size of the region to reserve, `ss` is the starting offset, and `[KMG]` specifies the size in Kilobytes, Megabytes, or Gigabytes. The configuration option is passed to the Kernel using GRUB. Changing GRUB menu entries and kernel arguments vary between Linux distributions and versions of the same distro. Instructions for some of the common Linux distros can be found below. Refer to the documentation of the Linux distro and version being used for more information.

The memory region will be marked as e820 type 12 \(0xc\) . This is visible at boot time. Use the dmesg command to view these messages.

```text
$ dmesg | grep e820
```

**Example:**

1. A GRUB entry of `memmap=4G!12G` reserves 4GB of memory starting at 12GB through 16GB.  See [How To Choose the Correct memmap Option for Your System](linux-memmap.md#how-to-choose-the-correct-memmap-option-for-your-system) for more details.  Each Linux distro has a different method for modifying the GRUB entries.  Follow the documentation for your distro.  A few of the common distros are provided below for quick reference.

{% tabs %}
{% tab title="Fedora" %}
Create a new memory mapping of 4GB starting at the 12GB boundary \(ie from 12GB to 16GB\)

**Fedora 23 or later:**

`$ grubby --args="memmap=4G\!12G" --update-kernel=ALL`

**Fedora 22 and earlier:**

```text
$ sudo vi /etc/default/grub
GRUB_CMDLINE_LINUX="memmap=4G!12G"
```

Update the grub config using one of the following methods:

On BIOS-based machines:

```text
$ sudo grub2-mkconfig -o /boot/grub2/grub.cfg
```

On UEFI-based machines:

```text
$ sudo grub2-mkconfig -o /boot/efi/EFI/centos/grub.cfg
```
{% endtab %}

{% tab title="Ubuntu" %}
Create a new memory mapping of 4GB starting at the 12GB boundary \(ie from 12GB to 16GB\)

`$ sudo vi /etc/default/grub`

Add or edit the "GRUB\_CMDLINE\_LINUX" entry to include the mapping, eg:

`GRUB_CMDLINE_LINUX="memmap=4G!12G"`

Then update grub and reboot

`$ sudo update-grub2`
{% endtab %}

{% tab title="RHEL & CentOS" %}
Create a new memory mapping of 4GB starting at the 12GB boundary \(ie from 12GB to 16GB\)

```text
$ sudo vi /etc/default/grub
GRUB_CMDLINE_LINUX="memmap=4G!12G"
```

Update the grub config using one of the following methods:

On BIOS-based machines:

`$ sudo grub2-mkconfig -o /boot/grub2/grub.cfg`

On UEFI-based machines:

`$ sudo grub2-mkconfig -o /boot/efi/EFI/centos/grub.cfg`
{% endtab %}
{% endtabs %}

{% hint style="info" %}
**Note:** If more than one persistent memory namespace is required, specify a memmap entry for each namespace. For example, "memmap=2G!12G memmap=2G!14G" will create two 2GB namespaces, one in the 12GB-14GB memory address offsets, the other at 14GB-16GB.
{% endhint %}

2. Reboot the host

3. After the host has been rebooted, a new `/dev/pmem{N}` device should exist, one for each memmap region specified in the GRUB config. These can be shown using `ls /dev/pmem*`. Naming convention starts at `/dev/pmem0` and increments for each device. The `/dev/pmem{N}` devices can be used to create a DAX filesystem.

4. Create and mount a filesystem using /dev/pmem device\(s\), then verify the `dax` flag is set for the mount point to confirm the DAX feature is enabled. The following shows how to create and mount an EXT4 or XFS filesystem.

{% tabs %}
{% tab title="EXT4" %}
```text
$ sudo mkfs.ext4 /dev/pmem0
$ sudo mkdir /pmem
$ sudo mount -o dax /dev/pmem0 /pmem
$ sudo mount -v | grep /pmem
/dev/pmem0 on /pmem type ext4 (rw,relatime,seclabel,dax,data=ordered)
```
{% endtab %}

{% tab title="XFS" %}
```text
$ sudo mkfs.xfs /dev/pmem0
$ sudo mkdir /pmem
$ sudo mount -o dax /dev/pmem0 /mnt/dax
$ sudo mount -v | grep /pmem
/dev/pmem0 on /pmem type xfs (rw,relatime,seclabel,attr2,dax,inode64,noquota)
```
{% endtab %}
{% endtabs %}

{% hint style="info" %}
**Note:** Refer to the [Advanced Topics](advanced-topics/) section for information on [Partitioning Namespaces](advanced-topics/partitioning-namespaces.md) and [I/O Alignment Considerations](advanced-topics/i-o-alignment-considerations.md) using hugepages.
{% endhint %}

## How To Choose the Correct memmap Option for Your System

When selecting values for the `memmap` kernel parameter, consideration that the start and end addresses represent usable RAM must be made. Using or overlapping with reserved memory can result in corruption or undefined behaviour. This information is easily available in the e820 table, available via dmesg.

The following shows an example server with 16GiB of memory with "usable" memory between 4GiB \(0x100000000\) and ~16GiB \(0x3ffffffff\):

```text
$ dmesg | grep BIOS-e820
[    0.000000] BIOS-e820: [mem 0x0000000000000000-0x000000000009fbff] usable
[    0.000000] BIOS-e820: [mem 0x000000000009fc00-0x000000000009ffff] reserved
[    0.000000] BIOS-e820: [mem 0x00000000000f0000-0x00000000000fffff] reserved
[    0.000000] BIOS-e820: [mem 0x0000000000100000-0x00000000bffdffff] usable
[    0.000000] BIOS-e820: [mem 0x00000000bffe0000-0x00000000bfffffff] reserved
[    0.000000] BIOS-e820: [mem 0x00000000feffc000-0x00000000feffffff] reserved
[    0.000000] BIOS-e820: [mem 0x00000000fffc0000-0x00000000ffffffff] reserved
[    0.000000] BIOS-e820: [mem 0x0000000100000000-0x00000003ffffffff] usable
```

To reserve the 12GiB usable space between 4GiB and 16GiB as emulated persistent memory, the syntax for this reservation will be as follows:

```text
memmap=12G!4G
```

After rebooting a new user defined e820 table entry shows the range is now "persistent \(type 12\)":

```text
$ dmesg | grep user:
[    0.000000] user: [mem 0x0000000000000000-0x000000000009fbff] usable
[    0.000000] user: [mem 0x000000000009fc00-0x000000000009ffff] reserved
[    0.000000] user: [mem 0x00000000000f0000-0x00000000000fffff] reserved
[    0.000000] user: [mem 0x0000000000100000-0x00000000bffdffff] usable
[    0.000000] user: [mem 0x00000000bffe0000-0x00000000bfffffff] reserved
[    0.000000] user: [mem 0x00000000feffc000-0x00000000feffffff] reserved
[    0.000000] user: [mem 0x00000000fffc0000-0x00000000ffffffff] reserved
[    0.000000] user: [mem 0x0000000100000000-0x00000003ffffffff] persistent (type 12)
```

The `fdisk` or `lsblk` utilities can be used to show the capacity, eg:

```text
# sudo fdisk -l /dev/pmem0 
Disk /dev/pmem0: 12 GiB,  12884901888 bytes, 25165824 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 4096 bytes
I/O size (minimum/optimal): 4096 bytes / 4096 bytes
```

```text
$ sudo lsblk /dev/pmem0
NAME  MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
pmem0 259:0    0  12G  0 disk /pmem
```

{% hint style="info" %}
**Note:** Most Linux distributions ship with Kernel Address Space Layout Randomization \(KASLR\) enabled. This is defined by `CONFIG_RANDOMIZE_BASE`. When enabled, the Kernel may potentially use memory previously reserved for persistent memory without warning, resulting in corruption or undefined behaviour. It is recommended to disable KASLR on systems with 16GiB or less. Refer to your Linux distribution documentation for details as the procedure varies per distro.
{% endhint %}

