---
description: Installing the Persistent Memory Development Kit
---

# Installing PMDK

## Introduction

The Persistent Memory Development Kit \(PMDK\) is available on Supported Operating Systems in package and source code formats. Some features of the PMDK require additional packages. Prerequisite packages and utilities are describe in the [Installing PMDK from Source on Linux](compiling-pmdk-from-source.md) section.

## Contents

### Linux

* [Installing PMDK using Linux Packages](installing-pmdk-using-linux-packages.md)
* [Installing PMDK from Source on Linux](compiling-pmdk-from-source.md)

### Windows

* [Installing PMDK on Windows](installing-pmdk-on-windows.md)

## PMDK Version Convention

PMDK delivers stable releases for production use, release candidates for early development and testing, and development builds for early access.

* **Builds** are tagged something like `0.2+b1`, which means _Build 1 on top of version 0.2_ 
* **Release Candidates** have a '-rc{version}' tag, eg `0.2-rc3`, which means _Release Candidate 3 for version 0.2_. 
* **Stable releases** use a _major.minor_ tag like `0.2`

