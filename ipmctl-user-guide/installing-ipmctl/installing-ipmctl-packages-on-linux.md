# Installing IPMCTL packages on Linux

The `ipmctl` utility is available in many Linux distribution package repositories.  This approach is the easiest to install and maintain, compared with [building and installing ipmctl from source](building-and-installing-ipmctl-from-source-on-linux.md). However, the version of ipmctl available in the package repository may not be current.

{% tabs %}
{% tab title="Fedora" %}
The ipmctl package for v02.00.00.xxxx or later is available in the default package repository on Fedora.

Step 1) Query the package repository to confirm that ipmctl is available:

```
dnf search ipmctl
dnf info ipmctl
```

Example:

```
$ dnf search ipmctl
Last metadata expiration check: 1:24:31 ago on Fri 27 Mar 2020 06:27:17 PM MDT.
================================================== Name Exactly Matched: ipmctl ==================================================
ipmctl.x86_64 : Utility for managing Intel Optane DC persistent memory modules
================================================= Name & Summary Matched: ipmctl =================================================
libipmctl-devel.x86_64 : Development packages for libipmctl
ipmctl-debugsource.x86_64 : Debug sources for package ipmctl
ipmctl-debuginfo.x86_64 : Debug information for package ipmctl
libipmctl-debuginfo.x86_64 : Debug information for package libipmctl
ipmctl-monitor-debuginfo.x86_64 : Debug information for package ipmctl-monitor
====================================================== Name Matched: ipmctl ======================================================
libipmctl.x86_64 : Library for Intel DCPMM management
ipmctl-monitor.x86_64 : Daemon for monitoring the status of Intel DCPMM

$  dnf info ipmctl
Last metadata expiration check: 1:25:19 ago on Fri 27 Mar 2020 06:27:17 PM MDT.
Installed Packages
Name         : ipmctl
Version      : 02.00.00.3446
Release      : 1.el7
Architecture : x86_64
Size         : 65 k
Source       : ipmctl-02.00.00.3446-1.el7.src.rpm
Repository   : @System
From repo    : jhli-ipmctl
Summary      : Utility for managing Intel Optane DC persistent memory modules
URL          : https://github.com/intel/ipmctl
License      : BSD
Description  : Utility for managing Intel Optane DC persistent memory modules
             : Supports functionality to:
             : Discover DCPMMs on the platform.
             : Provision the platform memory configuration.
             : View and update the firmware on DCPMMs.
             : Configure data-at-rest security on DCPMMs.
             : Monitor DCPMM health.
             : Track performance of DCPMMs.
             : Debug and troubleshoot DCPMMs.

```

Step 2) Install the ipmctl package:

```
sudo dnf install ipmctl
```

Step 3) Review the help and man pages or continue to the [Basic Usage](../basic-usage.md) section of this user guide for a quick introduction and more information.

```
$ sudo ipmctl help
```
{% endtab %}

{% tab title="RHEL & CentOS" %}
The ipmctl package is available on CentOS, RHEL, and RHEL for SAP HANA v7.5 or later in the EPEL repository (Extra Packages for Enterprise Linux).

Step 1) Verify the EPEL repository is active:

```
$ yum repolist
```

Example:

```
$ sudo yum repolist
repo id                                                       repo name                                                                                          status
epel/x86_64                                                   Extra Packages for Enterprise Linux 7 - x86_64                                                     13,217
```

If the EPEL repository is not listed, install and activate it using:

```
$ sudo yum install epel-release
```

Step 2) Query the package repository to confirm that ipmctl is available:

```
sudo yum info ipmctl
```

Example:

```
$ sudo yum info ipmctl 

Available Packages
Name        : ipmctl
Arch        : x86_64
Version     : 01.00.00.3474
Release     : 2.el7
Size        : 70 k
Repo        : epel/x86_64
Summary     : Utility for managing Intel Optane DC persistent memory modules
URL         : https://github.com/intel/ipmctl
License     : BSD
Description : Utility for managing Intel Optane DC persistent memory modules
            : Supports functionality to:
            : Discover DCPMMs on the platform.
            : Provision the platform memory configuration.
            : View and update the firmware on DCPMMs.
            : Configure data-at-rest security on DCPMMs.
            : Monitor DCPMM health.
            : Track performance of DCPMMs.
            : Debug and troubleshoot DCPMMs.
```

Step 3) Install the ipmctl package:

```
sudo yum install ipmctl
```

Step 4) Review the help and man pages or continue to the [Basic Usage](../basic-usage.md) section of this user guide for a quick introduction and more information.

```
$ sudo ipmctl help
```
{% endtab %}

{% tab title="SLES & OpenSUSE" %}
The ipmctl package is available in the default package repository on SUSE, OpenSUSE, and SUSE for SAP HANA 12.4 or later.

Step 1) Query the package repository to confirm that ipmctl is available:

```
sudo zypper info ipmctl
```

Example:

```
$ sudo zypper info ipmctl 

Information for package ipmctl:
-------------------------------
Repository     : SLES12-SP4-Updates                                            
Name           : ipmctl                                                        
Version        : 01.00.00.3440-3.8.2                                           
Arch           : x86_64                                                        
Vendor         : SUSE LLC <https://www.suse.com/>                              
Support Level  : Level 3                                                       
Installed Size : 3.2 MiB                                                       
Installed      : No                                                            
Status         : not installed                                                 
Source package : ipmctl-01.00.00.3440-3.8.2.src                                
Summary        : Utility for managing Intel Optane DC persistent memory modules
Description    :                                                               
    Utility for managing Intel Optane DC persistent memory modules
    Supports functionality to:
    * Discover PMMs on the platform.
    * Provision the platform memory configuration.
    * View and update the firmware on PMMs.
    * Configure data-at-rest security on PMMs.
    * Monitor PMM health.
    * Track performance of PMMs.
    * Debug and troubleshoot PMMs.
```

3\) Install the ipmctl package:

```
sudo zypper install ipmctl
```

4\) Review the help and man pages or continue to the [Basic Usage](../basic-usage.md) section of this user guide for a quick introduction and more information.

```
$ sudo ipmctl help
```
{% endtab %}

{% tab title="Ubuntu" %}
The ipmctl package for v02.00.00.xxxx or later is available in the Universe package repository on Ubuntu 19.10 (Eoan Ermine) or later.

Step 1) Query the package repository to confirm that ipmctl is available:

```
apt update
apt search ipmctl
apt info ipmctl
```

Example:

```
$ apt search ipmctl 
Sorting... Done
Full Text Search... Done
ipmctl/eoan 02.00.00.3474+really01.00.00.3469-1 amd64
  utility for configuring and managing Intel Optane DC persistent memory modules

ipmctl-monitor/eoan 02.00.00.3474+really01.00.00.3469-1 amd64
  daemon for monitoring health and status of Intel Optane DC modules

libipmctl-dev/eoan 02.00.00.3474+really01.00.00.3469-1 amd64
  library for managing Intel Optane DC persistent memory modules - devel

libipmctl3/eoan 02.00.00.3474+really01.00.00.3469-1 amd64
  library for managing Intel Optane DC persistent memory modules
  

$ apt info ipmctl 
Package: ipmctl
Version: 02.00.00.3474+really01.00.00.3469-1
Priority: optional
Section: universe/admin
Origin: Ubuntu
Maintainer: Ubuntu Developers <ubuntu-devel-discuss@lists.ubuntu.com>
Original-Maintainer: Adam Borowski <kilobyte@angband.pl>
Bugs: https://bugs.launchpad.net/ubuntu/+filebug
Installed-Size: 105 kB
Depends: libc6 (>= 2.2.5), libipmctl3 (>= 02.00.00.3474+really01.00.00.3469)
Homepage: https://github.com/intel/ipmctl
Download-Size: 65.5 kB
APT-Sources: http://us-central1.gce.archive.ubuntu.com/ubuntu eoan/universe amd64 Packages
Description: utility for configuring and managing Intel Optane DC persistent memory modules
 This package provides a CLI with the following functionality:
  * Discover PMMs on the platform.
  * Provision the platform memory configuration.
  * View and update the firmware on PMMs.
  * Configure data-at-rest security on PMMs.
  * Monitor PMM health.
  * Track performance of PMMs.
  * Debug and troubleshoot PMMs.
```



Step 2) Install the ipmctl package:

```
sudo apt install ipmctl
```

Step 3) Review the help and man pages or continue to the [Basic Usage](../basic-usage.md) section of this user guide for a quick introduction and more information.

```
$ sudo ipmctl help
```
{% endtab %}
{% endtabs %}

### Using ipmctl

Go to the [Basic Usage](../basic-usage.md) section of this user guide for more information.
