# Change Sensor Settings

Changes the non-critical threshold or enabled state for one or more persistent memory module sensors. Use the command [Show Sensor](show-sensor.md) to view the current settings.

```text
$ ipmctl set [OPTIONS] -sensor (SENSORS) [TARGETS] NonCriticalThreshold=(temperature) EnabledState=(0|1)
```

### **Sensors**

* `MediaTemperature`: The current module media temperature in Celsius. Valid values: 0-2047.
* `ControllerTemperature`: The current module controller temperature in Celsius. Valid values 0-2047.
* `PercentageRemaining`: Remaining module life as a percentage value of factory expected life span. Valid values: 1-99.

### **Targets**

* `dimm (DimmIDs)`: Show the sensors on specific modules by supplying one or more comma-separated DimmIDs. The default is to display sensors for all manageable modules.

### **Properties**

* `NonCriticalThreshold`: The upper \(for temperatures\) or lower \(for spare capacity\) non-critical alarm threshold of the sensor. If the current value of the sensor is at or above for thermal, or below for capacity, the threshold value, then the sensor will indicate a "NonCritical" state. Temperatures may be specified in degrees Celsius to a precision of 1/16 a degree.
* `EnabledState`: Enable or disable the non-critical threshold alarm. One of:
  * "0": Disable
  * "1": Enable

### **Examples** 

Change the media temperature threshold to 51 on the specified module and enable the alarm.

```text
ipmctl set -sensor MediaTemperature –dimm 0x0001 NonCriticalThreshold=51 EnabledState=1
```

