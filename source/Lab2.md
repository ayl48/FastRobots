# Lab 2: IMU

## Lab Tasks

### IMU Setup
Connect the SparkFun 9DOF IMU Breakout to the Artemis nano board using a Qwicc connector.
![](images/Lab1/IMU_connection.jpeg)

Running the basic example from the SparkFun_ICM-20948 library, the Artemis successfully retrieves readings from the accelerometer, magnetometer, and gyroscope, reflecting changes as I move the IMU.

<iframe width="560" height="315" src="https://www.youtube.com/embed/dr53VXYPOV8" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

In the example, AD0_VAL refers to the Address 0 bit that sets the sensor's I2C address. By default, when the ADR jumper is open, AD0_VAL is set to 1, giving the ICM an address of 0x69. When the ADR jumper is closed, AD0 is grounded, setting its value to 0 and changing the ICM's address to 0x68, so AD0_VAL should be updated in the code.

Working off provided IMU code, I make the board blink three times on startup to indicate that the IMU is running.

![](images/Lab1/blink_imu.PNG)



### Accelerometer

### Gyroscope

### Sample Data

### RC Car Stunt
I experimented with the RC car to understand its movement. It tends to skid or slide when stopping and sometimes jerks forward. It also skids when moving forward before turning. Its tight turns are almost 90 degrees.

2D movements (e.g. forward, backwards, turns, spins):
<iframe width="560" height="315" src="https://www.youtube.com/embed/UiRf5P_jSIE" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

Flip:
<iframe width="560" height="315" src="https://www.youtube.com/embed/Sz7C0u9L2Jk" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
