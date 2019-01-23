# Installing PMDK using Linux Packages

PMDK is upstreamed to many Linux distro package repositories including Fedora and Ubuntu.  Older versions of PMDK in RPM and DEB format are available from the [PMDK releases repository](https://github.com/pmem/pmdk/releases).

## Installing PMDK Using the Linux Distro Package Repository

The PMDK is a collection of different libraries, each one provides different functionality.  This provides greater flexibility for developers as only the required runtime or header files need to be installed without installing unnecessary libraries.  Libraries are available in runtime, development header files \(\*-devel\), and debug \(\*-debug\) versions.  For example:

| Library | Description |
| :--- | :--- |
| libpmem | Low-level persistent memory support library |
| libpmem-debug | Debug variant of the libpmem low-level persistent memory library |
| libpmem-devel | Development files for the low-level persistent memory library |

The following table shows the list of available libraries:

| Library | Description |
| :--- | :--- |
| libpmem | Low-level persistent memory support library |
| librpmem | Remote Access to Persistent Memory library |
| libpmemblk | Persistent Memory Resident Array of Blocks library |
| libpmemcto | Close-to-Open Persistence library \(Deprecated in PMDK v1.5\) |
| libpmemlog | Persistent Memory Resident Log File library |
| libpmemobj | Persistent Memory Transactional Object Store library |
| libpmempool | Persistent Memory pool management library |
| pmempool | Utilities for Persistent Memory |

{% tabs %}
{% tab title="Fedora" %}
1\) Query the repository to identify if pmdk is available:  

Fedora 21 or earlier

```text
   $ yum search pmem
```

Fedora 22 or later

```text
   $ dnf search pmem
```

2\) Install the pmdk packages

Fedora 21 or earlier

```text
$ yum install <library>
```

Fedora 22 or later

```text
$ dnf install <library>

All Runtime: 
$ dnf install libpmem librpmem libpmemblk libpmemlog libpmemobj libpmempool pmempool
All Development: 
$ dnf install libpmem-devel librpmem-devel libpmemblk-devel libpmemlog-devel libpmemobj-devel libpmemobj++-devel libpmempool-devel
All Debug:
$ dnf install libpmem-debug librpmem-debug libpmemblk-debug libpmemlog-debug libpmemobj-debug libpmempool-debug
```
{% endtab %}

{% tab title="RHEL & CentOS" %}
The pmdk package is available on CentOS and RHEL 7.0 or later.

1\) Query the repository to identify if pmdk is delivered:

```text
$ yum search pmem
```

2\) Install the pmdk packages

```text
$ yum install <library>

All Runtime: 
$ yum install libpmem librpmem libpmemblk libpmemlog libpmemobj libpmempool pmempool
All Development: 
$ yum install libpmem-devel librpmem-devel libpmemblk-devel libpmemlog-devel libpmemobj-devel libpmemobj++-devel libpmempool-devel
All Debug:
$ yum install libpmem-debug librpmem-debug libpmemblk-debug libpmemlog-debug libpmemobj-debug libpmempool-debug
```
{% endtab %}

{% tab title="SLES & OpenSUSE" %}
1\) Query the repository to identify if ndctl is delivered:

```text
$ zypper search pmem
```

2\) Install the ndctl packages

```text
$ zypper install <library>

All Runtime: 
$ zypper install libpmem librpmem libpmemblk libpmemlog libpmemobj libpmempool pmempool
All Development: 
$ zypper install libpmem-devel librpmem-devel libpmemblk-devel libpmemlog-devel libpmemobj-devel libpmemobj++-devel libpmempool-devel
All Debug:
$ zypper install libpmem-debug librpmem-debug libpmemblk-debug libpmemlog-debug libpmemobj-debug libpmempool-debug
```
{% endtab %}

{% tab title="Ubuntu" %}
The pmdk package is available on Ubuntu 18.10 \(Cosmic Cuttlefish\) or later.

1\) Query the repository to identify if ndctl is delivered using either the aptitude, apt-cache, or apt utilities

```text
$ aptitude search pmem
$ apt-cache search pmem
$ apt search pmem
```

2\) Install the pmdk packages

```text
$ apt-get install <library>

All Runtime: 
$ sudo apt-get install libpmem1 librpmem1 libpmemblk1 libpmemlog1 libpmemobj1 libpmempool1
All Development: 
$ sudo apt-get install libpmem-dev librpmem-dev libpmemblk-dev libpmemlog-dev libpmemobj-dev libpmempool-dev libpmempool-dev
All Debug:
$ sudo apt-get install libpmem1-debug librpmem1-debug libpmemblk1-debug libpmemlog1-debug libpmemobj1-debug libpmempool1-debug
```
{% endtab %}
{% endtabs %}

## Installing PMDK from \*.RPM or \*.DEB

PMDK is available in RPM and DEB package formats. The latest RPM and DEB package bundles can be downloaded from [https://github.com/pmem/pmdk/releases](https://github.com/pmem/pmdk/releases).  

{% hint style="info" %}
Since libraries are available in most Linux Distro repositories, the PMDK RPM and DEB packages will no longer be built for each release.  The following refers to PMDK v1.4 and earlier.
{% endhint %}

### Installing \*.RPM Packages

1\) Download the RPM bundle.

```text
$ mkdir -p /downloads/pmdk1.4
$ cd /downloads/pmdk1.4
$ wget 
$ tar zxf pmdk-1.4-rpms.tar.gz
```

2\) The download bundle includes an rpm with the source code and an `x86_64` sub-directory with installable packages

```text
$ ls -1R
.:
pmdk-1.4-1.fc25.src.rpm 
pmdk-1.4-rpms.tar.gz 
x86_64

./x86_64:
libpmem-1.4-1.fc25.x86_64.rpm
libpmemlog-devel-1.4-1.fc25.x86_64.rpm
<...snip...>
```

3\) Install the rpm packages with dependencies

```text
$ cd x86_64
$ dnf install *.rpm
```

### Upgrading Installed \*.RPM Packages

If PMDK was previously installed using the downloaded rpm packages, use the following to upgrade the installed packages.

{% hint style="info" %}
If you are upgrading from PMDK v1.3.1 \(formally NVML\) to PMDK 1.4 or later, the name change may cause package conflicts which causes some packages to fail. It is recommended to remove all nvml\* packages before trying to upgrade/install pmdk.

```text
$ dnf remove nvml*
```
{% endhint %}

1\) Download the latest RPM bundle from [https://github.com/pmem/pmdk/releases](https://github.com/pmem/pmdk/releases), eg 1.4.1

```text
$ mkdir -p /downloads/pmdk1.4.1
$ cd /downloads/pmdk1.4.1
$ wget 
$ tar zxf pmdk-1.4.1-rpms.tar.gz
```

2\) The download bundle includes an rpm with the source code and an `x86_64` sub-directory with installable packages

```text
$ ls -1R
.:
pmdk-1.4.1-1.fc25.src.rpm 
pmdk-1.4.1-rpms.tar.gz 
x86_64

./x86_64:
libpmem-1.4.1-1.fc25.x86_64.rpm
libpmemlog-devel-1.4.1-1.fc25.x86_64.rpm
<...snip...>
```

3\) Upgrade the packages using the `upgrade` or `install` sub-command

```text
$ cd x86_64
$ dnf upgrade *.rpm
- or -
$ dnf install *.rpm
```

### Installing \*.DEB Packages

1\) Download the pmdk-{version}-dpkgs.tar.gz from [https://github.com/pmem/pmdk/releases](https://github.com/pmem/pmdk/releases), eg to download PMDK v1.4:

```text
$ mkdir -p /downloads/pmdk1.4
$ cd /downloads/pmdk1.4
$ wget 
$ tar zxf pmdk-1.4-dpkgs.tar.gz
```

2\) The download bundle includes installable packages in the root

```text
$ ls -1
libpmem_1.4-1_amd64.deb
libpmemblk_1.4-1_amd64.deb
<...snip...>
```

3\) Install the packages

```text
$ sudo dpkg -i *.deb
```

### Upgrading Installed \*.DEB Packages

If PMDK was previously installed using the downloaded deb packages, use the following to upgrade the installed packages.

{% hint style="info" %}
If you are upgrading from PMDK v1.3.1 \(formally NVML\) to PMDK 1.4 or later, the name change may cause package conflicts which causes some packages to fail. It is recommended to remove all nvml\* packages before trying to upgrade/install pmdk.

```text
$ dpkg -r nvml*
```
{% endhint %}

1\) Download the pmdk-{version}-dpkgs.tar.gz from [https://github.com/pmem/pmdk/releases](https://github.com/pmem/pmdk/releases), eg to download PMDK v1.4.1:

```text
$ mkdir -p /downloads/pmdk1.4.1
$ cd /downloads/pmdk1.4.1
$ wget 
$ tar zxf pmdk-1.4.1-dpkgs.tar.gz
```

2\) The download bundle includes installable packages in the root

```text
$ ls -1
libpmem_1.4.1-1_amd64.deb
libpmemblk_1.4.1-1_amd64.deb
<...snip...>
```

3\) Install the packages

```text
$ sudo dpkg -u *.deb
```

