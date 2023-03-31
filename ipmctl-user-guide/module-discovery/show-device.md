# Show Device

Shows information about one or more Intel Optane DC memory modules.

```
ipmctl show [OPTIONS] -dimm [TARGETS]
```

## **Targets**

* `-dimm (DimmIDs)`: Restricts output to specific modules by supplying the DIMM target and one or more comma-separated DimmIDs. The default is to display all DCPMMs.
* `-socket (SocketIDs)`: Restricts output to the  modules installed on specific sockets by supplying the socket target and one or more comma-separated socket identifiers. The default is to display all sockets.
  * If ACPI PMTT table is not present, then DDR4 memory will not be displayed in the filtered socket list.

## **Examples**

List a few key fields for each persistent memory module.

```
$ sudo ipmctl show -dimm

 DimmID | Capacity  | HealthState | ActionRequired | LockState | FWVersion
==============================================================================
 0x0001 | 126.4 GiB | Healthy     | 0              | Disabled  | 01.02.00.5310
 0x0011 | 126.4 GiB | Healthy     | 0              | Disabled  | 01.02.00.5310
 0x0021 | 126.4 GiB | Healthy     | 0              | Disabled  | 01.02.00.5310
 0x0101 | 126.4 GiB | Healthy     | 0              | Disabled  | 01.02.00.5310
 0x0111 | 126.4 GiB | Healthy     | 0              | Disabled  | 01.02.00.5310
 0x0121 | 126.4 GiB | Healthy     | 0              | Disabled  | 01.02.00.5310
 0x1001 | 126.4 GiB | Healthy     | 0              | Disabled  | 01.02.00.5310
 0x1011 | 126.4 GiB | Healthy     | 0              | Disabled  | 01.02.00.5310
 0x1021 | 126.4 GiB | Healthy     | 0              | Disabled  | 01.02.00.5310
 0x1101 | 126.4 GiB | Healthy     | 0              | Disabled  | 01.02.00.5310
 0x1111 | 126.4 GiB | Healthy     | 0              | Disabled  | 01.02.00.5310
 0x1121 | 126.4 GiB | Healthy     | 0              | Disabled  | 01.02.00.5310
```

List all properties for module 0x0001.

```
$ sudo ipmctl show -a -dimm 0x0001
---DimmID=0x0001---
   Capacity=252.454 GiB
   LockState=Disabled
   S3Resume=Unknown
   HealthState=Healthy
   HealthStateReason=None
   FWVersion=01.02.00.5417
   FWAPIVersion=01.15
   InterfaceFormatCode=0x0301 (Non-Energy Backed Byte Addressable)
   ManageabilityState=Manageable
   PopulationViolationState=Is in supported configuration.
   PhysicalID=0x0026
   DimmHandle=0x0001
   DimmUID=8089-a2-1837-00000be8
   SocketID=0x0000
   MemControllerID=0x0000
   ChannelID=0x0000
   ChannelPos=1
   MemoryType=Logical Non-Volatile Device
   Manufacturer=Intel
   VendorID=0x8089
   DeviceID=0x5141
   RevisionID=0x0000
   SubsystemVendorID=0x8089
   SubsystemDeviceID=0x097a
   SubsystemRevisionID=0x0018
   DeviceLocator=CPU1_DIMM_A2
   ManufacturingInfoValid=1
   ManufacturingLocation=0xa2
   ManufacturingDate=18-37
   SerialNumber=0x00000be8
   PartNumber=NMA1XBD256GQS
   BankLabel=NODE 1
   DataWidth=64 b
   TotalWidth=72 b
   Speed=2666 MT/s
   FormFactor=DIMM
   ManufacturerID=0x8089
   ControllerRevisionID=B0, 0x0020
   MemoryCapacity=0.000 GiB
   AppDirectCapacity=252.000 GiB
   UnconfiguredCapacity=0.000 GiB
   InaccessibleCapacity=0.454 GiB
   ReservedCapacity=0.000 GiB
   PackageSparingCapable=1
   PackageSparingEnabled=1
   PackageSparesAvailable=1
   IsNew=0
   ViralPolicy=0
   ViralState=0
   PeakPowerBudget=20000 mW
   AvgPowerLimit=18000 mW
   MaxAveragePowerLimit=18000 mW
   LatchedLastShutdownStatus=PM ADR Command Received, DDRT Power Fail Command Received, PMIC 12V/DDRT 1.2V Power Loss (PLI), Controller's FW State Flush Complete, Write Data Flush Complete
   UnlatchedLastShutdownStatus=PMIC 12V/DDRT 1.2V Power Loss (PLI), PM Warm Reset Received, Controller's FW State Flush Complete, Write Data Flush Complete, PM Idle Received
   ThermalThrottleLossPercent=N/A
   LastShutdownTime=Sat May 30 00:49:03 UTC 2020
   ModesSupported=Memory Mode, App Direct
   SecurityCapabilities=Encryption, Erase
   MasterPassphraseEnabled=0
   ConfigurationStatus=Valid
   SKUViolation=0
   ARSStatus=Completed
   OverwriteStatus=Unknown
   AitDramEnabled=1
   BootStatus=Success
   BootStatusRegister=0x00000004_985d00f0
   ErrorInjectionEnabled=0
   MediaTemperatureInjectionEnabled=0
   SoftwareTriggersEnabled=0
   SoftwareTriggersEnabledDetails=None
   PoisonErrorInjectionsCounter=0
   PoisonErrorClearCounter=0
   MediaTemperatureInjectionsCounter=0
   SoftwareTriggersCounter=0
   MaxControllerTemperature=81 C
   MaxMediaTemperature=78 C
   MixedSKU=0

```

Retrieve specific properties for each module.

```
$ sudo ipmctl show -d HealthState,LockState -dimm 0x0001

---DimmID=0x0001---
   LockState=Disabled
   HealthState=Healthy
```

## **Return Data**

See the ipmctl-show-device(1) man page for a detailed explanation of all fields displayed by `ipmctl-show-device`.
