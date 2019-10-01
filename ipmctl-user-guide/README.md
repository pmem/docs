# IPMCTL User Guide

## Introduction

`ipmctl` is an open source utility created and maintained by Intel to manage Intel® Optane™ DC persistent memory modules. `ipmctl`, which works in both Linux and Windows, is a vendor specific tool, meaning it is not able to be used for managing NVDIMMs from vendors other than Intel. The full project is open source and can be seen on [GitHub](https://github.com/intel/ipmctl). In this guide we will refer to Intel® Optane™ DC memory modules simply as _modules_ or the _persistent memory modules_.

`ipmctl` refers to the following interface components:

* `libipmctl`: An Application Programming Interface \(API\) library for managing persistent memory modules.
* `ipmctl`: A Command Line Interface \(CLI\) application for configuring and managing persistent memory modules from the command line.
* `ipmctl-monitor`: A monitor daemon/system service for monitoring the health and status of persistent memory modules.

Functionality includes:

* Discover Intel Optane DC memory modules on the platform
* Provision the platform memory configuration
  * Learn more about operating modes in this [video](link)
* View and update module firmware
* Configure data-at-rest security
* Monitor module health
* Track performance of modules
* Debug and troubleshoot modules

Architecture Diagram: 

![](../.gitbook/assets/capture.PNG)

