# Installing PMDK from Source on Linux

## Overview

This procedure describes how to clone the source code from the pmdk github repository and compile, then install it.

If your system is behind a firewall and requires a proxy to access the Internet, configure your package manager to use a proxy.

## Install Prerequisites

To build the PMDK libraries on Linux, you may need to install the following required packages on the build system:

* autoconf
* automake
* doxygen
* gcc
* gcc-c++
* glib2
* glib2-devel
* graphviz
* libfabric
* libfabric-devel
* pandoc
* pkg-config
* ncurses

{% tabs %}
{% tab title="Fedora" %}
```text
$ sudo dnf install autoconf automake pkg-config glib2 glib2-devel libfabric libfabric-devel doxygen graphviz pandoc ncurses
```
{% endtab %}

{% tab title="Ubuntu & Debian" %}
```text
$ sudo apt install autoconf automake pkg-config libglib2.0-0 libglib2.0-dev libfabric1 libfabric-dev doxygen graphviz pandoc libncurses5
```
{% endtab %}
{% endtabs %}

The following packages are required only by selected PMDK components or features. If not present, those components or features may not be available:

* **libfabric** \(v1.4.2 or later\) -- required by **librpmem**
* **libndctl** and **libdaxctl** \(v60.1 or later\) -- required by **daxio** and RAS features.  See [Installing NDCTL](../installing-ndctl.md)
  * To build pmdk without ndctl support, set 'NDCTL\_ENABLE=n' using: `$ export NDCTL_ENABLE=n`

The `git` utility is required to clone the repository or you can download the source code as a [zip file](https://github.com/pmem/pmdk/archive/master.zip) directly from the [repository ](https://github.com/pmem/pmdk)on GitHub.

A C/C++ Compiler is required. GCC/G++ will be used in this documentation but you may use a different compiler then set the `CC` and `CXX` shell environments accordingly.

```text
$ sudo dnf install gcc gcc-c++
```

```text
$ sudo apt install gcc g++
```

To build and test the PMDK library on FreeBSD, you may need to install the following required packages on the build system:

* autoconf
* bash
* binutils
* coreutils
* doxygen
* e2fsprogs-libuuid
* gmake
* glib2
* glib2-devel
* libunwind
* ncurses\*
* pandoc
* pkgconf

```text
$ sudo pkg install autoconf automake bash coreutils doxygen e2fsprogs-libuuid graphviz glib2 glib2-devel gmake libunwind pandoc ncurses pkg-config
```

\(\*\) The pkg version of ncurses is required for proper operation; the base version included in FreeBSD is not sufficient.

The git utility is required to clone the repository or you can download the source code as a [zip ](https://github.com/pmem/pmdk/archive/master.zip)file directly from the [repository ](https://github.com/pmem/pmdk)on GitHub.

A C/C++ Compiler is required. GCC/G++ will be used in this documentation but you may use a different compiler then set the `CC` and `CXX` shell environments accordingly.

```text
$ sudo pkg install gcc gcc-c++
```



To build PMDK and run the tests you need:

* **MS Visual Studio 2015** or later
* [Windows SDK 10.0.16299.15](https://developer.microsoft.com/en-us/windows/downloads/windows-10-sdk) or later
* **perl** \(i.e. [ActivePerl](http://www.activestate.com/activeperl/downloads)\)
* **PowerShell 5** or later

## Clone the PMDK GitHub Repository

The following uses the `git` utility to clone the repository.

```text
$ sudo mkdir /downloads
$ sudo chmod +w /downloads
$ cd /downloads
$ git clone https://github.com/pmem/pmdk
$ cd pmdk
```

Alternatively you may download the source code as a [zip file](https://github.com/pmem/pmdk/archive/master.zip) from the [GitHub website](https://github.com/pmem/pmdk).

```text
$ sudo mkdir /downloads
$ sudo chmod +w /downloads
$ cd /downloads
$ wget 
$ unzip master.zip
$ cd pmdk-master
```

## Compile

{% tabs %}
{% tab title="Linux & FreeBSD" %}
To build the master branch run the `make` utility in the root directory:

```text
$ make
```

If you want to compile with a different compiler, you have to provide the `CC` and `CXX`variables. For example:

```text
$ make CC=clang CXX=clang++
```

These variables are independent and setting `CC=clang` does not set `CXX=clang++`.

{% hint style="info" %}
If the `make` command returns an error similar to the following, this is caused by pkg-config being unable to find the required "libndctl.pc" file.

```text
$ make
src/common.inc:370: *** libndctl(version >= 60.1) is missing -- see README.  Stop.
```

This can occur when ndctl was installed in a directory other than the /usr/bin default location.

To resolve this issue, the PKG\_CONFIG\_PATH is a environment variable that specifies additional paths in which pkg-config will search for its .pc files.

This variable is used to augment pkg-config's default search path. On a typical Unix system, it will search in the directories /usr/lib/pkgconfig and /usr/share/pkgconfig. This will usually cover system installed modules. However, some local modules may be installed in a different prefix such as /usr/local. In that case, it's necessary to prepend the search path so that pkg-config can locate the .pc files.

The pkg-config program is used to retrieve information about installed libraries in the system. The primary use of pkg-config is to provide the necessary details for compiling and linking a program to a library. This metadata is stored in pkg-config files. These files have the suffix .pc and reside in specific locations known to the pkg-config tool.

To check the PKG\_CONFIG\_PATH value use this command:

`$ echo $PKG_CONFIG_PATH`

To set the PKG\_CONFIG\_PATH value use:

```text
$ export PKG_CONFIG_PATH=/usr/local/lib64/pkgconfig:/usr/local/lib/pkgconfig:/usr/lib64/pkgconfig:/usr/lib/pkgconfig
```

Now execute the `make` command again.
{% endhint %}
{% endtab %}
{% endtabs %}

Once the make completes, all the libraries are built along with the examples under `src/examples`. You can experiment with the library within the build tree, or install it locally on your machine.

## Install

Installing the library is more convenient since it installs man pages and libraries in the standard system locations.

{% tabs %}
{% tab title="Linux & FreeBSD" %}
To install the libraries to the default `/usr` location:

```text
$ sudo make install
```

To install this library into other locations, you can use the `prefix=path` option, e.g:

```text
$ sudo make install prefix=/usr/local
```

To update all users shell environment, add the following to `/etc/profile.d/custom.sh` \(create the file if it doesn't already exist\). Note: This location may differ depending on the Linux Distribution you're using. Check the documentation for your specific distro and version.

```text
# PMDK
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/lib:/usr/local/lib64
```

Otherwise add to the users profile or .bashrc \(or the users shell equivalent environment file\)
{% endtab %}
{% endtabs %}

