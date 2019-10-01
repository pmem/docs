# Show Sensor

Shows health statistics for one or more persistent memory modules.

```text
$ ipmctl show [OPTOINS] -sensor [SENSORS] [TARGETS]
```

### **Sensors**

* `Health`: The current module health as reported in the SMART log
* `MediaTemperature`: The current module media temperature in Celsius
* `ControllerTemperature`: The current module controller temperature in Celsius
* `PercentageRemaining`: Remaining module life as a percentage value of factory expected life span
* `LatchedDirtyShutdownCount`: The number of shutdowns without notification over the lifetime of the module
* `Unlatched DirtyShutdownCount`: The number of shutdowns without notification over the lifetime of the module. This counter is the same as LatchedDirtyShutdownCount except it will always be incremented on a dirty shutdown, even if Latch System Shutdown Status was not enabled.
* `PowerOnTime`: The total power-on time over the lifetime of the module
* `UpTime`: The total power-on time since the last power cycle of the module
* `PowerCycles`: The number of power cycles over the lifetime of the module
* `FwErrorCount`: The total number of firmware error log entries

### **Targets**

* `dimm (DimmIDs)`: Show only the sensors on specific modules by supplying the DIMM target and one or more comma-separated DimmIDs. The default is to display sensors for all manageable modules.

### **Examples** 

Get all sensor information for all modules

```text
$ ipmctl show -sensor
```

Show the media temperature sensor for the specified module

```text
$ ipmctl show -sensor MediaTemperature -dimm 0x0011
```

### **Return Data**

* `DimmID`: The persistent memory module identifier
* `Type`: The sensor type. Refer to the Sensors list above
* `CurrentValue`: The current reading followed by the units of measurement \(e.g., 57 °C or 25%\)
* `CurrentState`: The current value in relation to the threshold settings \(if supported\). One of:
  * `Unknown`: The state cannot be determined
  * `Normal`: The current reading is within the normal range. This is the default when the sensor does not support thresholds
  * `NonCritical`: The current reading is within the non-critical range. For example, an alarm threshold has been reached
  * `Critical`: The current reading is within the critical range. For example, the firmware has begun throttling down traffic to the persistent memory module due to the temperature
  * `Fatal`: The current reading is within the fatal range. For example, the firmware is shutting down the module due to the temperature
* `LowerThresholdNonCritical`: The threshold value below which the state is considered "NonCritical".
* `UpperThresholdNonCritical`: The threshold value at or above which the state is considered "NonCritical".
* `LowerThresholdCritical`: The threshold value below which the state is considered "Critical".
* `UpperThresholdCritical`: The threshold value at or above which the state is considered "Critical".
* `UpperThresholdFatal`: The threshold value at or above which the state is considered "Fatal".
* `SettableThresholds`: A list of user settable thresholds. Zero or more of:
  * `LowerThresholdNonCritical`
  * `UpperThresholdNonCritical`
* `SupportedThresholds`: A list of supported thresholds. Zero or more of:
  * `LowerThresholdNonCritical`
  * `UpperThresholdNonCritical`
  * `LowerThresholdCritical`
  * `UpperThresholdCritical`
  * `UpperThresholdFatal`
* `EnabledState`: Whether the critical threshold alarm is enabled, disabled or not applicable. One of:
  * 0: Disabled
  * 1: Enabled
  * N/A

