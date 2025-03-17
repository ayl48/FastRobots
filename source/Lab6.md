# Lab 6: Orientation Control

## Prelab
In Lab 6, I built upon my Lab 5 code by using case statements for PID control and adjusting the Kp and Ki values.

### PID Input Signal
Remembering how much drift affected my yaw measurement in Lab 2, I decided to test out the Digital Motion Processor in the InvenSense ICM-20948 sensor instead. Here is a video of the DMP example code running with the visualizer.
<iframe width="560" height="315" src="https://www.youtube.com/embed/VRjVanpF9zI" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
___
The gyroscope data alone introduced drift over time due to the accumulation of small errors, which led to increasing orientation inaccuracies. While bias correction and calibration could help, the drift was still nontrivial. I switched to the DMP because it mitigates this by fusing data from the gyroscope, accelerometer, and magnetometer, reducing drift and providing more stable yaw measurements. This sensor fusion improves orientation tracking and helps stabilize the yaw value when the robot is stationary. However, enabling the DMP requires extra memory on the microcontroller and modifications to the SparkFun ICM-20948 Arduino library.

After making the decision to to switch from using the gyroscope data alone to using the DMP, I organized the DMP code into a separate header and CPP file like how I did with the ToF sensor code in Lab 5.

DMP.h is shown below.
![](images/Lab6/DMP_h.jpeg)

DMP.cpp is shown below.
![](images/Lab6/DMP_cpp1.jpeg)
![](images/Lab6/DMP_cpp2.jpeg)

The [quaternion](https://en.wikipedia.org/w/index.php?title=Conversion_between_quaternions_and_Euler_angles&section=8#Source_code_2) data is taken from the DMP and converted to Euler angles. In my DMP code, I calculate only yaw to maximize performance and minimize computational overhead since roll and pitch are not needed for this lab.

The DMP sends its data through a FIFO queue before I2C transmission. The queue must be read and emptied regularly to get the latest orientation readings. In order to prevent the FIFO from filling up due to slow reads, I reduce the DMP output rate to ~10Hz and avoid unnecessary delays, ensuring continuous polling unless the queue is empty.

DMP Output Rate Reduction:
![](images/Lab6/dmp_rate.jpeg)

Delaying Only When Queue is Empty:
![](images/Lab6/dmp_delay.jpeg)

### Bluetooth Commands
The Bluetooth commands I added for orientation PID are displayed. Like in Lab 5, they allow me to set parameters and control the start/stop of my orientation PID code from my computer.

I am reusing the **SET_PID_PARAM** command which sets PI parameters (Kp and Ki). This command can change the parameter values while my controller is running.

Arduino side:
![](images/Lab6/set_pid_param.jpeg)

Python side:
![](images/Lab5/SET_PID_py.jpeg)

I am also reusing the **SET_CONSTRAINT** command which allows me to choose PWM value limits to cap the turn speed. I can also set the flag for clamping here, which enables or disables wind-up protection for the integral term.

Arduino side:
![](images/Lab5/SET_CONSTRAINT.jpeg)

Python side:
![](images/Lab5/SET_CON_py.jpeg)

The **START_ORIENT_PID** command allows me to set the target angle () and PID runtime, then executes PI control for the specified duration. After executing the control, it sends timestamped data, including angles, PWM values, P terms, and I terms, to the computer for plotting. The PI control implementation will be covered in the next section.

I retained the hard stop from before in the main loop as a safety measure in case the Bluetooth connection fails.
![](images/Lab5/hard_stop.jpg)

### PI Discussion and Implementation
The following explanation was discussed in my Lab 5 webpage but is repeated here for context. "The PID equation and block digram from Professor Helbling's slides are shown.
![](images/Lab5/PID_equation.jpeg)
![](images/Lab5/PID_diagram.jpeg)

In essence, the PID control equation combines three components to adjust a system's behavior. The Proportional term responds to the current error, the Integral term accounts for past errors to eliminate steady-state error, and the Derivative term predicts future errors based on the rate of change. Together, these components work to minimize error and improve system stability and accuracy."

------
Like lab 5, I implemented only PI control because, through experimentation, I found it sufficient to make the robot turned to the target angle. The P term provided effective control, while the I term helped with fine-tuning the small and persistent errors. Since the robot is turning so slowly, and I'm lightly kicking the robot as opposed to punting it across the room, there are no substantial fluctuations or fast changes in the system that would necessitate the D term.

I implemented my **START_ORIENT_PID** code very similarly to my linear PID code, I use a while loop to implement PI control for the desired duration. I compute the time step (dt) for the integral control and then update the PI values using the orient_pid function. At the end of the loop, I manage the turn direction based on the sign of the adjusted PWM value from the PI calculation.

Arduino side:
![](images/Lab6/start_orient_ard1.jpg)
![](images/Lab6/start_orient_ard2.jpg)

Python side:
![](images/Lab6/start_orient_pid.jpeg)

In my **orient_pid** function located in the orient.cpp file, I implement PI according to the equation.

orient.h:
![](images/Lab6/orient_h.jpeg)

orient.cpp:
![](images/Lab6/orient_cpp.jpeg)

I also include my clamping code for wind-up protection in my orient_pid function. The clamp flag, activated in **SET_CONSTRAINT**, controls the conditional that encompasses the clamping code.

### Programming Implementation Notes
The  cannot currently be adjusted while the robot is running or moving forward/backward, but I may add this functionality in a future lab through a new command to allow real-time modifications. This command can be easily implemented by simply modifying the current target_angle variable in the code. This variable is passed as an argument when the orient_pid function is called. Real-time  adjustments are useful for stunts, enabling dynamic orientation control during complex movements like flips or sharp turns, helping the robot reach the desired angle at the right moment.

## Lab Tasks

### Orientation Control
In all my testing, I set 50 degrees as my setpoint or target angle.

#### Proportional (P) Control
First, I implemented proportional control. Somehow I got lucky and guessed a good Kp value on the first try.

Kp = 0.0001.

![](images/Lab6/p_angle.jpeg)
![](images/Lab6/p_pwm.jpeg)
![](images/Lab6/p_pterm.jpeg)

<iframe width="560" height="315" src="https://www.youtube.com/embed/nsWOYTPYxV4" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

___
#### Proportional Integral (PI) Control
After determining Kp, I initially attempted to follow the heuristic procedure from the slides by increasing Ki until overshoot occurred and then gradually decreasing it until the overshoot disappeared. However, I ultimately had to rely on trial and error to find a suitable combination of Kp and Ki. My main focus was on the car's ability to return to the setpoint, using that as the primary indicator of effective tuning.

Kp = 0.5 and Ki = 0.0005

<iframe width="560" height="315" src="https://www.youtube.com/embed/rpV5EgI7W3g" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
___

![](images/Lab6/pi_noclamp_angle.jpeg)
![](images/Lab6/pi_noclamp_pwm.jpeg)
![](images/Lab6/pi_noclamp_pterm.jpeg)
![](images/Lab6/pi_noclamp_iterm.jpeg)

### Derivative Term Discussion
Although, I did not use a derivate term in my controller, I will address the discussion points written in the handout on a high level. Taking the derivative of an integrated signal is valid because it reflects how the accumulated error changes over time, allowing for a response based on past behavior. However, this can cause derivative kick when the  changes suddenly, leading to large spikes in the derivative term. To mitigate this, it's better to apply the derivative to the error directly and use a low-pass filter to smooth out high-frequency noise.

### Sampling Frequency and Performance of DMP
Using the same methods from previous labs, I calculated the frequency at which the DMP could return new data: ~45 Hz.
![](images/Lab6/DMP_freq.jpeg)

I conducted a test to evaluate the effect of motor noise on the DMP data. By placing the car on the ground, I recorded angle measurements with the motors both on and off. The graph below shows that the DMP is highly resistant to noise, as the angle measurements differ by only about 1 degree between the two conditions.
![](images/Lab6/noise_test.jpeg)

### Wind-Up Protection for Integrator

#### No Wind-up Protection
The integrator term in my controller caused a wind-up issue as expected. The accumulated error increased rapidly and could not shrink fast enough as the robot approached the target angle. This issue caused my car to spin in circles. Surprisingly, my car still ended up really close to the 50 degree setpoint in the end.

![](images/Lab6/pi_noclamp2_angle.jpeg)
![](images/Lab6/pi_noclamp2_pwm.jpeg)
![](images/Lab6/pi_noclamp2_pterm.jpeg)
![](images/Lab6/pi_noclamp2_iterm.jpeg)

<iframe width="560" height="315" src="https://www.youtube.com/embed/7Q-QikCo2gM" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
_____

#### Wind-Up Protection
Like lab 5, I implemented clamping in the code below using the logic shown in Professor Helbling's slides to fix the wind-up issue. The accumulated error is reset to 0 when the controller is clamped, helping to prevent oversaturation.
![](images/Lab5/clamp_slide.jpg)

![](images/Lab5/clamp_code.jpg)

![](images/Lab6/pi_clamp_angle.jpeg)
![](images/Lab6/pi_clamp_pwm.jpeg)
![](images/Lab6/pi_clamp_pterm.jpeg)
![](images/Lab6/pi_clamp_iterm.jpeg)

<iframe width="560" height="315" src="https://www.youtube.com/embed/kqI4PpA5Q6U" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

___
## References
I heavily referenced Professor Helbling's slides. I also referenced my own lab 5 page. I discussed ideas with Becky.
