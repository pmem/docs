# Partitioning Namespaces

It is possible to partition raw, sector, and fsdax devices \(namespaces\) using tools such as fdisk, parted, or gparted, etc. This is useful to meet application space requirements.

The following shows how to partition a new namespace with no existing partition table using fdisk and parted. If an existing partition table exists, delete or modify the entries first. The example uses a 256GB FSDAX device \(namespace\) and creates 2 x 100GB and 1 x ~50GB partitions on which filesystems can be created.

{% hint style="danger" %}
**WARNING:** Data could or will be lost. Backup the data before proceeding.
{% endhint %}

Print the current partition table, if any, using `fdisk -l`:

```text
$ sudo fdisk -l /dev/pmem0
Disk /dev/pmem0: 245.1 GiB, 263182090240 bytes, 514027520 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 4096 bytes
I/O size (minimum/optimal): 4096 bytes / 4096 bytes
```

{% tabs %}
{% tab title="fdisk" %}
1\) Launch fdisk for the device \(/dev/pmem0\)

```text
$ sudo fdisk /dev/pmem0
Welcome to fdisk (util-linux 2.32.1). Changes will remain in memory only, until you decide to write them. Be careful before using the write command.
Device does not contain a recognized partition table. Created a new DOS disklabel with disk identifier 0xc637b85f.
Command (m for help):
```

2\) Create the first new 100GB partition using the '\(n\)ew' command

```text
Command (m for help): n 
Partition type 
    p primary (0 primary, 0 extended, 4 free) 
    e extended (container for logical partitions) 
Select (default p): p 
Partition number (1-4, default 1): 
First sector (2048-514027519, default 2048): 
Last sector, +sectors or +size{K,M,G,T,P} (2048-514027519, default 514027519): +100G
Created a new partition 1 of type 'Linux' and of size 100 GiB.
```

3\) Create the second new 100GB partition using the '\(n\)ew' command:

```text
Command (m for help): n 
Partition type 
    p primary (1 primary, 0 extended, 3 free) 
    e extended (container for logical partitions) 
Select (default p):

Using default response p. 
Partition number (2-4, default 2): 
First sector (209717248-514027519, default 209717248): 
Last sector, +sectors or +size{K,M,G,T,P} (209717248-514027519, default 514027519): +100G
Created a new partition 2 of type 'Linux' and of size 100 GiB.
```

4\) Create the last partition using the remaining space:

```text
Command (m for help): n 
Partition type 
    p primary (2 primary, 0 extended, 2 free) 
    e extended (container for logical partitions) Select (default p):

Using default response p. 
Partition number (3,4, default 3): 
First sector (419432448-514027519, default 419432448): 
Last sector, +sectors or +size{K,M,G,T,P} (419432448-514027519, default 514027519):
Created a new partition 3 of type 'Linux' and of size 45.1 GiB.
```

5\) Print the new partition table to verify the changes using the '\(p\)rint' command:

```text
Command (m for help): p 
Disk /dev/pmem0: 245.1 GiB, 263182090240 bytes, 514027520 sectors 
Units: sectors of 1 * 512 = 512 bytes 
Sector size (logical/physical): 512 bytes / 4096 bytes 
I/O size (minimum/optimal): 4096 bytes / 4096 bytes 
Disklabel type: dos 
Disk identifier: 0xc637b85f

Device       Boot     Start       End   Sectors  Size Id Type
/dev/pmem0p1           2048 209717247 209715200  100G 83 Linux
/dev/pmem0p2      209717248 419432447 209715200  100G 83 Linux
/dev/pmem0p3      419432448 514027519  94595072 45.1G 83 Linux
```

6\) Commit the changes using the '\(w\)rite' command and return to the shell prompt:

```text
Command (m for help): w
The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks.

#
```
{% endtab %}

{% tab title="parted" %}
1\) Launch parted and select the device \(/dev/pmem0\)

```text
$ sudo parted /dev/pmem0
GNU Parted 3.2
Using /dev/pmem0
Welcome to GNU Parted! Type 'help' to view a list of commands.
(parted)
```

2\) Create the first new 100GB partition using the 'mkpart' command

```text
(parted) mkpart
Partition type?  primary/extended? p
File system type?  [ext2]? ext4
Start? 2MiB
End? 100GiB
(parted)
```

3\) Create the second new 100GB partition using the 'mkpart' command:

```text
(parted) mkpart
Partition type?  primary/extended? p
File system type?  [ext2]? ext4
Start? 100GiB
End? 200GiB
```

4\) Create the last partition using the remaining space \(-1MiB\):

```text
(parted) mkpart
Partition type?  primary/extended? p
File system type?  [ext2]? xfs
Start? 200GiB
End? -1MiB
(parted) p
```

5\) Print the new partition table to verify the changes using the 'print' command:

```text
(parted) p
Model: NVDIMM Device (pmem)
Disk /dev/pmem0: 263GB
Sector size (logical/physical): 512B/4096B
Partition Table: msdos
Disk Flags:

Number  Start   End    Size    Type     File system  Flags
 1      2097kB  107GB  107GB   primary  ext4         lba
 2      107GB   215GB  107GB   primary  ext4         lba
 3      215GB   263GB  48.4GB  primary  xfs          lba
```

6\) Quit parted and return to the shell prompt:

```text
(parted) q
Information: You may need to update /etc/fstab.
```
{% endtab %}
{% endtabs %}

The partitions can now be used with DAX enabled filesystems such as EXT4 and XFS and mounted with the `-o dax` option. The following shows how to create and mount an EXT4 or XFS filesystem.

{% tabs %}
{% tab title="EXT4" %}
```text
$ sudo mkfs.ext4 /dev/pmem0p1
$ sudo mkdir /pmem1
$ sudo mount -o dax /dev/pmem0p1 /pmem1
$ sudo mount -v | grep /pmem1
/dev/pmem0p1 on /pmem1 type ext4 (rw,relatime,seclabel,dax,data=ordered)
```
{% endtab %}

{% tab title="XFS" %}
```text
$ sudo mkfs.xfs /dev/pmem0p1
$ sudo mkdir /pmem1
$ sudo mount -o dax /dev/pmem0p1 /mnt/dax
$ sudo mount -v | grep /pmem1
/dev/pmem0p1 on /pmem1 type xfs (rw,relatime,seclabel,attr2,dax,inode64,noquota)
```
{% endtab %}
{% endtabs %}

