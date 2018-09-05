# Using QEMU Virtualization

[QEMU ](https://www.qemu.org/)is a free and open-source hypervisor that performs hardware virtualization. The virtual NVDIMM \(vNVDIMM\) feature was introduced in QEMU v2.6.0. Only the memory type \(pmem\) has been implemented. The block window type \(blk\) is an outstanding feature enhancement to be implemented in the future. Refer to the QEMU release notes for any changes.

## Installing QEMU

QEMU is available as prebuilt binaries for most Linux Distributions, Mac OS, and Windows. Install instructions can be found on the [QEMU download page](https://www.qemu.org/download) and shown below:

{% tabs %}
{% tab title="ARCH Linux" %}
```text
$ pacman -S qemu
```
{% endtab %}

{% tab title="Debian/Ubuntu" %}
```text
$  apt-get install qemu
```
{% endtab %}

{% tab title="Fedora" %}
```text
$  dnf install @virtualization
```
{% endtab %}

{% tab title="RHEL/CentOS" %}
```text
$ yum install qemu-kvm
```
{% endtab %}

{% tab title="SUSE" %}
```text
$ zypper install qemu
```
{% endtab %}

{% tab title="MAC OS" %}
QEMU can be installed from **Homebrew**:

```text
$ brew install qemu
```

QEMU can be installed from **MacPorts**:

```text
$ sudo port install qemu
```

QEMU requires Mac OS X 10.5 or later, but it is recommended to use Mac OS X 10.7 or later.
{% endtab %}

{% tab title="Windows" %}
Stefan Weil provides binaries and installers for both [32-bit](https://qemu.weilnetz.de/w32/) and [64-bit](https://qemu.weilnetz.de/w64/) Windows.
{% endtab %}
{% endtabs %}

## Quick Start

The storage of a vNVDIMM device in QEMU is provided by the memory backend \(i.e. memory-backend-file and memory-backend-ram\). The `-object` describes the location, size, and id of the memory-backend and `-device` maps the `-object` to the guest. A simple way to create a single vNVDIMM device at startup time is done via the following command line options:

```text
 -machine pc,nvdimm
 -m $RAM_SIZE,slots=$N,maxmem=$MAX_SIZE
 -object memory-backend-file,id=mem1,share=on,mem-path=$PATH,size=$NVDIMM_SIZE
 -device nvdimm,id=nvdimm1,memdev=mem1
```

Where,

* the `nvdimm` machine option enables vNVDIMM feature.
* `slots=$N` should be equal to or larger than the total amount of normal RAM devices and vNVDIMM devices, e.g. $N should be &gt;= 2 here.

  If the HotPlug feature is required, assign enough slots for the eventual total number of RAM and vNVDIMM devices.

* `maxmem=$MAX_SIZE` should be equal to or larger than the total size

   of normal RAM devices and vNVDIMM devices, e.g. $MAX\_SIZE should be

   &gt;= $RAM\_SIZE + $NVDIMM\_SIZE here.

* `object memory-backend-file,id=mem1,share=on,mem-path=$PATH,size=$NVDIMM_SIZE` creates a backend storage of size `$NVDIMM_SIZE` on a file `$PATH`. All

   accesses to the virtual NVDIMM device go to the file `$PATH`.

  * `share=on/off` controls the visibility of guest writes. If

     `share=on`, then guest writes will be applied to the backend

     file. If another guest uses the same backend file with option

     `share=on`, then above writes will be visible to it as well. If

     `share=off`, then guest writes won't be applied to the backend

     file and thus will be invisible to other guests.

* `device nvdimm,id=nvdimm1,memdev=mem1` creates a virtual NVDIMM

   device whose storage is provided by above memory backend device.

Multiple vNVDIMM devices can be created if multiple pairs of `-object` and `-device` are provided. See Example 1 below.

**Example 1: Creating a simple Guest with 2 vNVDIMMs**

The following example runs a Fedora 27 guest with two 4GiB vNVDIMMs, 4GiB of DDR Memory, 4 vCPUs, a VNC Server on port 0 for console access, and ssh traffic redirected from port 2222 on the host to port 22 in the guest for direct ssh access.

```text
# qemu-system-x86_64 -drive file=/virtual-machines/qemu/fedora27.raw,format=raw,index=0,media=disk \
  -m 4G,slots=4,maxmem=32G \
  -smp 4 \
  -machine pc,accel=kvm,nvdimm=on \
  -enable-kvm \
  -vnc :0 \
  -net nic \
  -net user,hostfwd=tcp::2222-:22 \
  -object memory-backend-file,id=mem1,share,mem-path=/virtual-machines/qemu/f27nvdimm0,size=4G \
  -device nvdimm,memdev=mem1,id=nv1,label-size=2M \
  -object memory-backend-file,id=mem2,share,mem-path=/virtual-machines/qemu/f27nvdimm1,size=4G \
  -device nvdimm,memdev=mem2,id=nv2,label-size=2M \
  -daemonize
```

For a detailed description of the options shown above, and many others, refer to the [QEMU User's Guide](https://qemu.weilnetz.de/doc/qemu-doc.html).

{% hint style="info" %}
**Note:** Example 1 uses an existing Operating System Image. When creating a brand new QEMU guest with a blank OS disk image file, an ISO will need to be presented to the guest from which the OS can then be installed. A guest can access a local or remote ISO using:

**Local ISO:**

```text
--drive media=cdrom,file=/downloads/Fedora-Server-dvd-x86_64-28-1.1.iso,readonly
```

**Remote ISO**

```text
--drive media=cdrom,file=,readonly
```

Use a VNC Viewer to access the console to perform a manual installation.
{% endhint %}

## NVDIMM Labels

Labels contain metadata to describe the NVDIMM features and namespace configuration. Labels are stored on each NVDIMM in a reserved area called the 'Label Storage Area'. The exact location of the label storage area is NVDIMM-specific. QEMU v2.7.0 and later store labels at the end of backend storage. If a memory backend file, which was previously used as the backend of a vNVDIMM device without labels, is now used for a vNVDIMM device with label, the data in the label area at the end of file will be inaccessible to the guest. If any useful data \(e.g. the meta-data of the file system\) was stored there, the latter usage may result guest data corruption \(e.g. breakage of guest file system\).

QEMU v2.7.0 and later implement the label support for vNVDIMM devices. To enable label on vNVDIMM devices, users can simply add "label-size=$LBLSIZE" option to "-device nvdimm", e.g.

```text
-device nvdimm,id=nvdimm1,memdev=mem1,label-size=256K
```

{% hint style="info" %}
**Note:**

The minimal label size is 128KB. which is enough to store approximately 1000 labels. Labels are never overwritten in-place. New labels or updates to existing labels are written to new labels slots within the label storage area.
{% endhint %}

Label information can be accessed using the `ndctl` command utility which needs to be installed within the guest. See the '[NDCTL Users Guide](../../../ndctl-users-guide/)' for more details.

## NVDIMM HotPlug Feature

QEMU v2.8.0 and later implement the hotplug support for dynamically adding vNVDIMM devices to a running guest. Similarly to the RAM hotplug, the vNVDIMM hotplug is accomplished by two monitor console commands "object\_add" and "device\_add".

When QEMU is running, a monitor console is provided for performing interactive operations on the guest. Using the commands available in the monitor console, it is possible to inspect the running operating system, change removable media, take screenshots or audio grabs and control several other aspects of the virtual machine.

If the guest uses VNC, as Example 1 showed with `-vnc :0`, connect to the guest using a VNC Viewer and access the monitor using `Ctrl-Alt-2` \(and `Ctrl-Alt-1` to get back to the VM display\). Alternatives include:

* virsh qemu-monitor-command
* Using telnet.  The guest will need to be started with `-qmp tcp:localhost:4444,server --monitor stdio`.  To connect, use `telnet localhost 4444`.
* Using UNIX Sockets. Start the guest with `-qmp unix:./qmp-sock,server --monitor stdio` and connect using `nc -U ./qmp-sock`.

The QMP protocol used by the monitor is JSON based. When using the telnet or UNIX sockets, commands must be passed in a correctly formatted JSON strings. See the '[QMP](https://wiki.qemu.org/Documentation/QMP)' protocol documentation for more information.

For example, the following commands add another 4GB vNVDIMM device to the guest using the qemu monitor interface. The `(qemu)` monitor prompt is accessed via a vnc connection to the host using the guest vnc port, then pressing Ctrl-Alt-2, as described above.

```text
 (qemu) object_add memory-backend-file,id=mem3,share=on,mem-path=/virtual-machines/qemu/f27nvdimm2,size=4G
 (qemu) device_add nvdimm,id=nvdimm2,memdev=mem3
```

{% hint style="info" %}

**Note:**

1. Each hotplugged vNVDIMM device consumes one memory slot. Users should always ensure the memory option "-m ...,slots=N" specifies enough number of slots, i.e.

   N &gt;= number of RAM devices +

   ```text
      number of statically plugged vNVDIMM devices +

      number of hotplugged vNVDIMM devices
   ```

2. The similar is required for the memory option "-m ...,maxmem=M", i.e.

   M &gt;= size of RAM devices +

   ```text
      size of statically plugged vNVDIMM devices +

      size of hotplugged vNVDIMM devices
   ```

More detailed information about the HotPlug feature can be found in the [QEMU Memory HotPlug Documentation](https://github.com/qemu/qemu/blob/master/docs/memory-hotplug.txt).

## NVDIMM IO Alignment

QEMU uses mmap\(2\) to maps vNVDIMM backends and aligns the mapping address to the page size \(getpagesize\(2\)\) by default. However, some types of backends may require an alignment different than the page size. In that case, QEMU v2.12.0 and later provide 'align' option to memory-backend-file to allow users to specify the proper alignment.

For example, device dax require the 2 MB alignment, so we can use following QEMU command line options to use `/dev/dax0.0` as the backend of vNVDIMM:

```text
 -object memory-backend-file,id=mem1,share=on,mem-path=/dev/dax0.0,size=4G,align=2M
 -device nvdimm,id=nvdimm1,memdev=mem1
```

