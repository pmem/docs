# Installing NDCTL

The `ndctl` utility is used to manage the libnvdimm \(non-volatile memory device\) sub-system in the Linux Kernel. It is required for several Persistent Memory Developer Kit \(PMDK\) features if compiling from source. If ndctl is not available, the PMDK may not build all components and features. This page describes how to install ndctl using the Linux package repository or compiled from source code downloaded from the [ndctl GitHub repository](https://github.com/pmem/ndctl).

{% hint style="info" %}
Microsoft Windows users can read the [NVM documentation](https://docs.microsoft.com/en-us/windows/desktop/persistent-memory-programming-in-windows---nvml-integration).
{% endhint %}

## Installing NDCTL Packages on Linux

The ndctl utility is available in several Linux distro repositories.

{% tabs %}
{% tab title="Fedora" %}
1\) Query the repository to identify if ndctl is delivered:

Fedora 21 or earlier

```text
yum search ndctl
```

Fedora 22 or later

```text
dnf repoquery ndctl
```

2\) Install the ndctl utility

Fedora 21 or earlier

```text
yum install ndctl
```

Fedora 22 or later

```text
dnf install ndctl
```
{% endtab %}

{% tab title="RHEL & CentOS" %}
The ndctl package is available on CentOS and RHEL 7.0 or later.

1\) Query the repository to identify if ndctl is delivered:

```text
yum search ndctl
```

2\) Install the ndctl package

```text
$ yum install ndctl
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
The ndctl package is available on Ubuntu 18.10 \(Cosmic Cuttlefish\) or later.

1\) Query the repository to identify if ndctl is delivered using either the aptitude, apt-cache, or apt utilities

```text
$ aptitude search ndctl 
$ apt-cache search ndctl 
$ apt search ndctl
```

2\) Verify if the ndctl package is currently installed and check the version

```text
$ apt list --installed ndctl
```

3\) Install the ndctl package or update an installed package

```text
$ sudo apt-get install ndctl
```
{% endtab %}

{% tab title="Debian" %}
{% hint style="info" %}
The ndctl package is available on Debian 10 \(Buster\) or later. See [https://tracker.debian.org/pkg/ndctl](https://tracker.debian.org/pkg/ndctl) for up to date information.
{% endhint %}

1\) Query available versions in configured repos:

```text
$ apt policy ndctl
```

2\) Query the repository to identify if ndctl is delivered using either the aptitude, apt-cache, or apt utilities

```text
$ apt search ndctl
```

3\) Verify if the ndctl package is currently installed and check the version

```text
$ apt list --installed ndctl
```

4\) Install the ndctl package or update an installed package

```text
$ sudo apt install ndctl
```
{% endtab %}
{% endtabs %}

## Installing NDCTL from Source on Linux

Utility library for managing the libnvdimm \(non-volatile memory device\) sub-system in the Linux kernel and is not available for Windows.

If your system is behind a firewall and requires a proxy to access the Internet, configure dnf to use a proxy. Edit /etc/dnf/dnf.conf and add the following line

```text
proxy=http://url:port/
```

You must set a complete URL, including the TCP port number. If your proxy server requires a username and password, specify these by adding following two settings in dnf.conf file.

```text
proxy_username=YOUR-PROXY-USERNAME-HERE
proxy_password=YOUR-SUPER-secrete-PASSWORD-HERE
```

### 1. Install Prerequisites

There are a number of packages required for the build steps that may not be installed by default. For information about the required packages, see the "BuildRequires:" lines in ndctl.spec.in.

[https://github.com/pmem/ndctl/blob/master/ndctl.spec.in](https://github.com/pmem/ndctl/blob/master/ndctl.spec.in)

To successfully compile ndctl from source with documentation, the following packages are required

* autoconf 
* automake 
* asciidoc \(asciidoctor\)
* bash-completion
* doxygen 
* gcc 
* gcc-c++ \(g++\)
* git
* glib2 
* glib2-devel 
* graphviz 
* json-c-devel
* keyutils-libs-devel \(libkeyutils-dev\)
* kmod 
* kmod-devel 
* libfabric 
* libfabric-devel 
* libtool 
* libudev-devel 
* libuuid-devel
* ncurses 
* pandoc 
* pkg-config 
* rubygem-asciidoctor \(asciidoctor\)
* xmlto

To install these prerequisites, use:

{% tabs %}
{% tab title="Fedora" %}
Fedora 21 or earlier

```text
sudo yum install git gcc gcc-c++ autoconf automake asciidoc asciidoctor xmlto libtool pkg-config glib2 glib2-devel libfabric libfabric-devel doxygen graphviz pandoc ncurses kmod kmod-devel libudev-devel libuuid-devel json-c-devel keyutils-libs-devel
```

Fedora 22 or later

```text
sudo dnf install git gcc gcc-c++ autoconf automake asciidoc asciidoctor xmlto libtool pkg-config glib2 glib2-devel libfabric libfabric-devel doxygen graphviz pandoc ncurses kmod kmod-devel libudev-devel libuuid-devel json-c-devel keyutils-libs-devel
```
{% endtab %}

{% tab title="RHEL & CentOS" %}
Some of the required packages can be found in the EPEL repository. Verify the EPEL repository is active:

```text
$ yum repolist
```

If the EPEL repository is not listed, install and activate it using:

```text
$ sudo yum install epel-release
```

Install the required packages

```text
$ sudo yum install git gcc gcc-c++ autoconf automake asciidoc bash-completion xmlto libtool pkgconfig glib2 glib2-devel libfabric libfabric-devel doxygen graphviz pandoc ncurses kmod kmod-devel libudev-devel libuuid-devel json-c-devel rubygem-asciidoctor keyutils-libs-devel
```
{% endtab %}

{% tab title="SLES & OpenSUSE" %}
```text
$ sudo zypper install -y git gcc gcc-c++ autoconf automake asciidoc bash-completion xmlto libtool pkg-config glib2 glib2-devel libfabric libfabric-devel doxygen graphviz pandoc ncurses kmod kmod-devel libudev-devel libuuid-devel json-c-devel rubygem-asciidoctor keyutils-libs-devel
```
{% endtab %}

{% tab title="Ubuntu & Debian" %}
**For Ununtu 18.04 \(Bionic\) and Debian 9 \(Stretch\) or later:**

```text
$ sudo apt install -y git gcc g++ autoconf automake asciidoc asciidoctor bash-completion xmlto libtool pkg-config libglib2.0-0 libglib2.0-dev libfabric1 libfabric-dev doxygen graphviz pandoc libncurses5 libkmod2 libkmod-dev libudev-dev uuid-dev libjson-c-dev libkeyutils-dev
```

**For Ubuntu 16.04 \(Xenial\) and Debian 8 \(Jessie\):**

{% hint style="info" %}
Earlier releases of Ubuntu and Debian do not have libfabric1 or libfabric-dev available in the repository. If these libraries are required, you should compile them yourself. See [https://github.com/ofiwg/libfabric](https://github.com/ofiwg/libfabric)
{% endhint %}

```text
$ sudo apt-get install -y git gcc g++ autoconf automake asciidoc asciidoctor bash-completion xmlto libtool pkg-config libglib2.0-0 libglib2.0-dev doxygen graphviz pandoc libncurses5 libkmod2 libkmod-dev libudev-dev uuid-dev libjson-c-dev libkeyutils-dev
```
{% endtab %}
{% endtabs %}

### 2. Clone the GitHub Repository

2.1\) If you're behind a company proxy, configure git to work with your proxy server first. The following configures a HTTP and HTTPS proxy for all users. Refer to the [git-config documentation](https://git-scm.com/docs/git-config) for more options and information.

```text
$ git config --global http.proxy http://proxyUsername:proxyPassword@proxy.server.com:port

$ git config --global https.proxy https://proxyUsername:proxyPassword@proxy.server.com:port
```

2.2\) Create a working directory to clone the ndctl GitHub repository to, eg: 'downloads'

```text
$ sudo mkdir /downloads
$ sudo chmod +w /downloads
```

2.3\) Clone the repository:

```text
$ cd /downloads
$ sudo git clone https://github.com/pmem/ndctl
$ cd ndctl
```

### 3. Build

The following configures ndctl to be installed in to the /usr/local directory.

```text
$ sudo ./autogen.sh
$ sudo ./configure CFLAGS='-g -O2' --prefix=/usr/local --sysconfdir=/etc --libdir=/usr/local/lib64
$ sudo make
```

#### **Build using an alternative compiler**

**Note:** If you want to compile with a different compiler other than gcc, you have to provide the CC and CXX environment variables. For example:

```text
$ sudo make CC=clang CXX=clang++
```

These variables are independent and setting CC=clang does not set CXX=clang++.

#### Build a Debug Version

To compile ndctl with debugging, use the `--enable-debug` option:

```text
$ sudo ./autogen.sh
$ sudo ./configure CFLAGS='-g -O2' --enable-debug --prefix=/usr/local --sysconfdir=/etc --libdir=/usr/local/lib64
$ sudo make
```

For a full list of configure options use:

```text
$ ./configure --help
```

### 4. Install

Once ndctl has successfully been compiled, it can be installed using the following:

```text
$ sudo make install
```

### 5. Build and Run Unit Tests \(Optional\)

The unit tests run by `make check` require the nfit\_test.ko module to be loaded. To build and install nfit\_test.ko:

1. Obtain the kernel source. For example, `git clone -b libnvdimm-for-next git://git.kernel.org/pub/scm/linux/kernel/git/nvdimm/nvdimm.git`
2. Skip to step 3 if the kernel version is &gt;= v4.8. Otherwise, for kernel versions &lt; v4.8, configure the kernel to make some memory available to CMA \(contiguous memory allocator\). This will be used to emulate DAX. `CONFIG_DMA_CMA=y` `CONFIG_CMA_SIZE_MBYTES=200` **or** `cma=200M` on the kernel command line.
3. Compile the libnvdimm sub-system as a module, make sure "zone device" memory is enabled, and enable the btt, pfn, and dax features of the sub-system: `CONFIG_X86_PMEM_LEGACY=m` `CONFIG_ZONE_DEVICE=y` `CONFIG_LIBNVDIMM=m` `CONFIG_BLK_DEV_PMEM=m` `CONFIG_ND_BLK=m` `CONFIG_BTT=y` `CONFIG_NVDIMM_PFN=y` `CONFIG_NVDIMM_DAX=y` `CONFIG_DEV_DAX_PMEM=m`
4. Build and install the unit test enabled libnvdimm modules in the following order. The unit test modules need to be in place prior to the `depmod` that runs during the final `modules_install` `make M=tools/testing/nvdimm` `sudo make M=tools/testing/nvdimm modules_install` `sudo make modules_install`
5. Now run `make check` in the ndctl source directory, or `ndctl test`, if ndctl was built with `--enable-test`.

### Troubleshooting

The unit tests will validate that the environment is set up correctly before they try to run. If the platform is misconfigured, i.e. the unit test modules are not available, or the test versions of the modules are superseded by the "in-tree/production" version of the modules `make check` will skip tests and report a message like the following in test/test-suite.log:

```text
SKIP: libndctl
==============
test/init: nfit_test_init: nfit.ko: appears to be production version: /lib/modules/4.8.8-200.fc24.x86_64/kernel/drivers/acpi/nfit/nfit.ko.xz
__ndctl_test_skip: explicit skip test_libndctl:2684
nfit_test unavailable skipping tests
```

If the unit test modules are indeed available in the modules 'extra' directory the default depmod policy can be overridden by adding a file to /etc/depmod.d with the following contents:

```text
override nfit * extra
override device_dax * extra
override dax_pmem * extra
override libnvdimm * extra
override nd_blk * extra
override nd_btt * extra
override nd_e820 * extra
override nd_pmem * extra
```

The nfit\_test module emulates pmem with memory allocated via vmalloc\(\). One of the side effects is that this breaks 'physically contiguous' assumptions in the driver. Use the `--align=4K` option to `ndctl create-namespace` to avoid these corner case scenarios.

### Using ndctl

To learn how to use the `ndctl` utility, continue to the [NDCTL Users Guide](../ndctl-users-guide/) within this collection.

### Additional Documentation

See the latest documentation for the NVDIMM kernel sub-system here: [https://git.kernel.org/cgit/linux/kernel/git/nvdimm/nvdimm.git/tree/Documentation/nvdimm/nvdimm.txt?h=libnvdimm-for-next](https://git.kernel.org/cgit/linux/kernel/git/nvdimm/nvdimm.git/tree/Documentation/nvdimm/nvdimm.txt?h=libnvdimm-for-next)

