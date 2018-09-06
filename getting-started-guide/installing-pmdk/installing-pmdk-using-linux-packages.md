# Installing PMDK using Linux Packages

PMDK is upstreamed to many Linux distro package repositories including Fedora and Ubuntu. Downloadable RPM and DEB packages for manual installation are also available from the [PMDK releases repository](https://github.com/pmem/pmdk/releases).

## Installing PMDK Using the Package Repository

{% tabs %}
{% tab title="Fedora" %}
1. Query the repository to identify if pmdk is delivered:  

Fedora 21 or earlier

```text
   $ yum search pmdk
```

Fedora 22 or later

```text
   $ dnf repoquery pmdk
```

1. Install the ndctl utility

Fedora 21 or earlier

```text
$ yum install pmdk
```

Fedora 22 or later

```text
$ dnf install pmdk
```
{% endtab %}

{% tab title="RHEL & CentOS" %}
The pmdk package is available on CentOS and RHEL 7.0 or later.

1\) Query the repository to identify if pmdk is delivered:

```text
$ yum search pmdk
```

2\) Install the ndctl package

```text
$ yum install pmdk
```
{% endtab %}

{% tab title="SLES & OpenSUSE" %}
1\) Query the repository to identify if ndctl is delivered:

```text
$ zypper search ndctl
```

2\) Install the ndctl package

```text
$ zypper install ndctl
```
{% endtab %}

{% tab title="Ubuntu" %}
The pmdk package is available on Ubuntu 18.10 \(Cosmic Cuttlefish\) or later.

1\) Query the repository to identify if ndctl is delivered using either the aptitude, apt-cache, or apt utilities

```text
$ aptitude search ndctl 
$ apt-cache search ndctl 
$ apt search ndctl
```

2\) Install the ndctl package

```text
$ apt-get install ndctl
```
{% endtab %}
{% endtabs %}

## Installing PMDK from \*.RPM or \*.DEB

PMDK is available in RPM and DEB package formats. The latest RPM and DEB package bundles can be downloaded from [https://github.com/pmem/pmdk/releases](https://github.com/pmem/pmdk/releases).

### Installing \*.RPM Packages

1. Download the RPM bundle.

```text
$ mkdir -p /downloads/pmdk1.4
$ cd /downloads/pmdk1.4
$ wget 
$ tar zxf pmdk-1.4-rpms.tar.gz
```

1. The download bundle includes an rpm with the source code and an `x86_64` sub-directory with installable packages

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

1. Install the rpm packages with dependencies

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

1. Download the latest RPM bundle from [https://github.com/pmem/pmdk/releases](https://github.com/pmem/pmdk/releases), eg 1.4.1

```text
$ mkdir -p /downloads/pmdk1.4.1
$ cd /downloads/pmdk1.4.1
$ wget 
$ tar zxf pmdk-1.4.1-rpms.tar.gz
```

1. The download bundle includes an rpm with the source code and an `x86_64` sub-directory with installable packages

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

1. Upgrade the packages using the `upgrade` or `install` sub-command

```text
$ cd x86_64
$ dnf upgrade *.rpm
- or -
$ dnf install *.rpm
```

### Installing \*.DEB Packages

1. Download the pmdk-{version}-dpkgs.tar.gz from [https://github.com/pmem/pmdk/releases](https://github.com/pmem/pmdk/releases), eg to download PMDK v1.4:

```text
$ mkdir -p /downloads/pmdk1.4
$ cd /downloads/pmdk1.4
$ wget 
$ tar zxf pmdk-1.4-dpkgs.tar.gz
```

1. The download bundle includes installable packages in the root

```text
$ ls -1
libpmem_1.4-1_amd64.deb
libpmemblk_1.4-1_amd64.deb
<...snip...>
```

1. Install the packages

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

1. Download the pmdk-{version}-dpkgs.tar.gz from [https://github.com/pmem/pmdk/releases](https://github.com/pmem/pmdk/releases), eg to download PMDK v1.4.1:

```text
$ mkdir -p /downloads/pmdk1.4.1
$ cd /downloads/pmdk1.4.1
$ wget 
$ tar zxf pmdk-1.4.1-dpkgs.tar.gz
```

1. The download bundle includes installable packages in the root

```text
$ ls -1
libpmem_1.4.1-1_amd64.deb
libpmemblk_1.4.1-1_amd64.deb
<...snip...>
```

1. Install the packages

```text
$ sudo dpkg -u *.deb
```

