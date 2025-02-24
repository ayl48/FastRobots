# Lab 3: Time of Flight (ToF) Sensors

## Prelab

**I²C address from [datasheet](https://cdn.sparkfun.com/assets/8/9/9/a/6/VL53L0X_DS.pdf):** 0x52

Since the I²C address is hardwired and both ToF sensors share the same address, I cannot address them individually without modifications. I wrote the second sensor's shutdown pin (XSHUT) low to disable it, changed the address of the first sensor, and then re-enabled the second sensor again to avoid address conflicts since they share the same bus.

Definitions:
![](images/Lab3/Tof_object.jpeg)

Setup function:
![](images/Lab3/Tof_setup.jpeg)


### Sensor Placement
I will mount one sensor on the front and another at a right angle on the side to expand the field of view, enhance depth perception, and improve multi-directional object detection.

![](images/Lab3/sensor_placement.jpg)

### Wiring Diagram
![](images/Lab3/ToFLab3Schematic.jpg)

## Lab Tasks

The ToF sensors are connected as shown below. The Artemis board can be powered with a battery.
![](images/Lab3/physical_hookup.jpg)

### Artemis Scanning for I2C Devices
![](images/Lab3/i2c_scan.jpeg)
The scanned address in the serial monitor does not match the datasheet because it shows only the part of the address that identifies the device, excluding the LSB, which indicates read/write.

### Sensor Data for Selected Distance Mode
I opted for the short-distance mode for the ToF sensors due because of its resolution at shorter distances and sufficient 1.3m range for the robot's navigation. It is also more immune to ambient light interference. In contrast, the long-distance mode can measure up to 4m but showed reduced accuracy under the bright lab lights during testing.

![](images/Lab3/sensor_distance_mode.jpg)

### Two ToF Sensors and IMU Working in Parallel

## Two ToF Sensors


### Time Vs. Distance

### Time Vs. Angle

### Discussion
##Infrared transmission based sensors
## Sensitivity to Colors and Textures

# References
I referenced Wenyi's and Nila's pages for help with formatting content.
