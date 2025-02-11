# Lab 2: IMU

## IMU Setup
Connect the SparkFun 9DOF IMU Breakout to the Artemis nano board using a Qwicc connector.
![](images/Lab2/IMU_connection.jpeg)

Running the basic example from the SparkFun_ICM-20948 library, the Artemis successfully retrieves readings from the accelerometer, magnetometer, and gyroscope, reflecting changes as I move the IMU.

<iframe width="560" height="315" src="https://www.youtube.com/embed/dr53VXYPOV8" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

In the example, AD0_VAL refers to the Address 0 bit that sets the sensor's I2C address. By default, when the ADR jumper is open, AD0_VAL is set to 1, giving the ICM an address of 0x69. When the ADR jumper is closed, AD0 is grounded, setting its value to 0 and changing the ICM's address to 0x68, so AD0_VAL should be updated in the code.

Working off provided IMU code, I make the board blink three times on startup to indicate that the IMU is running.

<iframe width="560" height="315" src="https://www.youtube.com/embed/nsbZK_xvh08" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>


## Accelerometer

### Roll and Pitch Outputs
The pitch and roll outputs of the accelerometer at {-90, 0, 90} are shown.

<iframe width="560" height="315" src="https://www.youtube.com/embed/T1_F63LQtd8" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

Here are the pitch and roll outputs when the accelerometer is flat on the table.
![Accelerometer flat](images/Lab2/accel_data.jpeg)  

The accelerometer is accurate within a few degrees. I used a box and table to position the IMU, but filming and holding it in place was challenging. Nevertheless, the output was accurate within a few degrees, so a two-point calibration wasn't necessary. However, I tried it for experience, using {0, 0, 0} as the other reference point.

To analyze the motion characteristics, I collected roll and pitch values from the accelerometer. I used the equations below to calculate them from the accelerometer's readings along its three axes.

![Roll and Pitch Equations](images/Lab2/a_equations.jpeg)

Since the accelerometer is susceptible to noise, I analyzed the roll and pitch noise in both the time and frequency domains. To examine its frequency spectrum, I applied a Fourier transform to the time-domain data using the code below.

![FFT Code](images/Lab2/FFT_code.jpeg)

### Still IMU Roll and Pitch Data
I placed the IMU flat on the table to collect the following noise data. Looking at the graphs below, a cut off frequency around 12-15 Hz is reasonable.

![Pitch Time](images/Lab2/pitcha_time_still.jpeg)
![Pitch Freq](images/Lab2/pitcha_freq_still.jpeg)


![Roll Time](images/Lab2/rolla_time_still.jpeg)
![Roll Freq](images/Lab2/rolla_freq_still.jpeg)

#### Noisy IMU Roll and Pitch
I placed the IMU on a notepad and gently bounced it on the table to collect the following data.

#### Pitch Data
![Pitch Time](images/Lab2/pitcha_time_noise.jpeg)
![Pitch Freq](images/Lab2/pitcha_freq_noise.jpeg)

### Roll Data
![Roll Time](images/Lab2/rolla_time_noise.jpeg)
![Roll Freq](images/Lab2/rolla_freq_noise.jpeg)

### Effect of Lowpass Filter
To filter the data, I used a low pass filter and implemented with the equations shown. The lowpass filter was extremely effective.
![LPF](images/Lab2/LPF.jpeg)

#### Still IMU

![Roll Time](images/Lab2/rolla_timeoverlay_still.jpeg)
![Roll Freq](images/Lab2/rolla_freqoverlay_still.jpeg)



![Pitch Time](images/Lab2/pitcha_timeoverlay_still.jpeg)
![Pitch Freq](images/Lab2/pitcha_freqoverlay_still.jpeg)

#### Noisy IMU

![Roll Time](images/Lab2/rolla_timeoverlay_noise.jpeg)
![Roll Freq](images/Lab2/rolla_freqoverlay_noise.jpeg)


![Pitch Time](images/Lab2/pitcha_timeoverlay_noise.jpeg)
![Pitch Freq](images/Lab2/pitcha_freqoverlay_noise.jpeg)

## Gyroscope
Using the following equations, I can calculate roll, pitch, and yaw from the gyroscope.

![Gyro Basic](images/Lab2/gyro_raw_code.jpeg)

Below are the data outputs from the gyroscope as it moves.

<iframe width="560" height="315" src="https://www.youtube.com/embed/jgirFcWsy7Y" frameborder="0" allowfullscreen></iframe>

The gyroscope data appears to be less noisy than the accelerometer data and shows a consistent offset across all values. While the data may not be entirely accurate, it remains stable, making it more suitable for detecting changes over time. It remains susceptible to drift, which can cause gradual and cumulative inaccuracies unless corrected.

Since gyroscopes are stable for short-term rotations but prone to drift, they can be paired with accelerometers that provide long-term stability but are affected by noise, so a complementary filter combines both for accurate and stable orientation tracking.

Complimentary Filter Equation:
![Complimentary Filter Equation](images/Lab2/complementary.jpeg)

I determined the optimal alpha value through trial and error. The ideal alpha value will vary for each sensor, as differences in manufacturing can affect sensor characteristics.



## Sampling Data

It’s better to use separate arrays for accelerometer and gyroscope data, as it simplifies processing and parsing, especially when transmitting data. Floats are ideal for sensor readings due to their precision and smaller memory usage compared to doubles, while ints work well for non-decimal data like timestamps. Given the 384KB memory on the Artemis, you can store up to 96,000 samples or about 16 minutes of data at a 100Hz sampling rate, assuming no other significant memory usage. This setup ensures efficient storage and easy data handling.

The video and the screenshot of the timestamps below demonstrate that my Artemis board can capture at least five seconds worth of data and send it over Bluetooth to my computer.


## RC Car Stunt
I experimented with the RC car to understand its movement. It tends to skid or slide when stopping and sometimes jerks forward. It also skids when moving forward before turning. Its tight turns are almost 90 degrees.

2D movements (e.g. forward, backwards, turns, spins):
<iframe width="560" height="315" src="https://www.youtube.com/embed/UiRf5P_jSIE" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

Flip:
<iframe width="560" height="315" src="https://www.youtube.com/embed/Sz7C0u9L2Jk" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
