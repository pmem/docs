# Installing PMDK on Windows

The recommended and easiest way to install PMDK on Windows is to use a Microsoft vcpkg. Vcpkg is an open source tool and ecosystem created for libraries management. PMDK requires following:

* MS Visual Studio 2015 or later
* Windows SDK 10.0.16299.15 or later
* perl \(i.e. ActivePerl\)
* PowerShell 5 or later

To install the latest PMDK release and link it to your Visual Studio solution at first you need to clone and set up vcpkg on your machine as described on the [vcpkg github page](https://github.com/Microsoft/vcpkg) in **Quick Start** section. Run the following within the powershell:

```text
> git clone https://github.com/Microsoft/vcpkg
> cd vcpkg
> .\bootstrap-vcpkg.bat
> .\vcpkg integrate install
> .\vcpkg install pmdk:x64-windows
```

{% hint style="info" %}
**Note:** The last command can take several minutes while it is builds and installs PMDK.
{% endhint %}

After successful completion of all of the above steps, libraries are ready to be used in Visual Studio. No additional configuration is required. Create a new project or open an existing project within Visual Studio \(remember to use platform **x64**\), then include the PMDK headers in your project.

## Additional Resources

* [Persistent Memory Programming in Windows - NVML Integration](https://docs.microsoft.com/en-us/windows/desktop/persistent-memory-programming-in-windows---nvml-integration) \(NVML was renamed to PMDK\)

