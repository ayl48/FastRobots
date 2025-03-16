# Lab 6: Orientation Control

## Prelab
I developed my Lab 6 code based on my Lab 5 code, where I use case statements to initiate PID control and adjust the Kp and Ki values with another case statement. Instead of relying on gyroscope data for yaw, I used the Digital Motion Processor (DMP) since it is less prone to drift, ensuring a stable yaw value when the robot is stationary.

### Bluetooth Commands
The Bluetooth commands I added for orientation PID are displayed. Like in Lab 5, they allow me to set parameters and control the start and stop of my orientation PID code from my computer.

I am reusing the **SET_PID_PARAM** command which sets PI parameters (Kp and Ki) and activates a tweak flag, that adjusts the PI calculation. This adjustment is discussed later in the implementation.

Arduino side:
![](images/Lab5/SET_PID_PARAM.jpeg)

Python side:
![](images/Lab5/SET_PID_py.jpeg)

I am also reusing the **SET_CONSTRAINT** command which allows me to choose PWM value limits to cap the turn speed. I can also set the flag for clamping here, which enables or disables wind-up protection for the integral term.

Arduino side:
![](images/Lab5/SET_CONSTRAINT.jpeg)

Python side:
![](images/Lab5/SET_CON_py.jpeg)

The **START_ORIENT_PID** command allows me to set the target angle (set point) and PID runtime, then executes PI control for the specified duration. The PI implementation will be discussed further in the implementation section. Lastly, it sends timestamped data, including angles, PWM values, P terms, and I terms, to the computer afterward for plotting.

Arduino side:
![](images/Lab6/START_ORIENT_PID_ard.jpg)
![](images/Lab6/START_ORIENT_PID_ard1.jpg)
![](images/Lab6/START_ORIENT_PID_ard2.jpg)

Python side:
![](images/Lab6/notif.jpeg)

![](images/Lab6/START_ORIENT_PID_py.jpg)

I retained the hard stop from before in the main loop as a safety measure in case the Bluetooth connection fails.
![](images/Lab5/hard_stop.jpg)

### PI Discussion and Implementation
The following explanation was discussed in my Lab 5 webpage but is repeated here for context. "The PID equation and block digram from Professor Helbling's slides are shown.
![](images/Lab5/PID_equation.jpeg)
![](images/Lab5/PID_diagram.jpeg)

In essence, the PID control equation combines three components to adjust a system's behavior. The Proportional term responds to the current error, the Integral term accounts for past errors to eliminate steady-state error, and the Derivative term predicts future errors based on the rate of change. Together, these components work to minimize error and improve system stability and accuracy."

Like lab 5, I implemented only PI control because, through experimentation, I found it sufficient to make the robot turned to the target angle. The P term provided effective control, while the I term helped with fine-tuning the small and persistent errors. Since the robot is turning so slowly, and I'm lightly kicking the robot as opposed to punting it across the room, there are no substantial fluctuations or fast changes in the system that would necessitate the D term.

I implemented my START_ORIENT_PID code very similarly to my linear PID code, I use a while loop to implement PI control for the desired duration. I compute the time step (dt) for the integral control and then update the PI values using the orient_pid function. At the end of the loop, I manage the turn direction based on the sign of the adjusted PWM value from the PI calculation.

![](images/Lab5/START_RUN_ard1.jpg)

In my **orient_pid** function, I implement PI according to the equation.

I also include my clamping code for wind-up protection in my orient_pid function. The clamp flag, activated in SET_CONSTRAINT, controls the conditional that encompasses the clamping code.

![](images/Lab5/lin_pid.jpg)
![](images/Lab5/helper_function.jpeg)


## Lab Tasks

### Orientation Control

#### Proportional (P) Control
First, I implemented proportional control. I started with an estimate by calculating the following:
![](images/Lab5/kp_estimate.jpg).
I rearranged the equation for PWM control and estimated Kp using the maximum PWM I allowed and the maximum error, based on the ToF sensor's 4m range.As always, the theoretical value requires adjustment to be suitable for practical application. I ended up tweaking this value to be lower through experimentation.

Kp = 0.04.

<iframe width="560" height="315" src="https://www.youtube.com/embed/6NMo0ybRPp8"
    frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture"
    allowfullscreen>
</iframe>


___
#### Proportional Integral (PI) Control
After finding Kp, I loosely followed heuristic 1 in the slides and increased Ki until overshoot and then slowly reduced the Ki until it was gone. I primarily looked at the car's ability to reach the 1-foot mark with precision and accuracy, using this as a key indicator of effective tuning. I did end up lowering Kp as well after experimenting with the values further through trial and error.

PI control without clamping: Kp = 0.032 and Ki = 0.01

![](images/Lab5/P_control/p_distance.jpeg)
![](images/Lab5/P_control/p_pwm.jpeg)
![](images/Lab5/P_control/p_pterm.jpeg)

<iframe width="560" height="315" src="https://www.youtube.com/embed/x92iKCiqwtM" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

![](images/Lab5/PI_control/pi_noclamp_distance.jpeg)
![](images/Lab5/PI_control/pi_noclamp_pwm.jpeg)
![](images/Lab5/PI_control/pi_noclamp_pterm.jpeg)
![](images/Lab5/PI_control/pi_noclamp_iterm.jpeg)

Two other runs of my PI control are shown.

<iframe width="560" height="315" src="https://www.youtube.com/embed/TsQqXjffLiI" frameborder="0" allowfullscreen></iframe>

<iframe width="560" height="315" src="https://www.youtube.com/embed/44w_pQgWc-4" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

___
### PID Input Signal
Using the same methods from previous labs, I calculated the frequency at which the DMP could return new data: ~.
![](images/Lab6/10Hz.jpeg)

After decoupling the ToF frequency from the PID loop by running the loop regardless of , I calculated the PID loop frequency to be ~121.25 Hz. To decouple the frequencies, I made the loop frequency dependent on the PID calculation rather than the sensor updates. When new ToF data is available, I updated the motor speed estimate; otherwise, I used the last saved value for PID calculations. This maintained continuous control without being constrained by the sensor's update rate.

#### Extrapolation Using Previous Datapoint
I extrapolated data using by using the previous data point if the ToF sensors is not currently ready with a reading. (Note: This is the loop I used to measure the decoupled PID loop frequency.)

![](images/Lab5/prev_data_code.jpeg)

#### Linear Interpolation
I implemented the linear interpolation equation below which uses the previous two datapoints to predict the next if the ToF sensor is not ready with a reading.
![](images/Lab5/interpolation_eq.png)
![](images/Lab5/interpolate_code.jpeg)

The graph for the raw data and the extrapolated data is shown.
![](images/Lab5/interpolate_graph.jpeg)

### Wind-Up Protection for Integrator
The integrator term in my controller caused a wind-up issue. The accumulated error increased rapidly and could not shrink fast enough as the robot approached the target distance. This issue caused my car to drive directly into the cabinet as shown below.

#### No Wind-up Protection
<iframe width="560" height="315" src="https://www.youtube.com/embed/ITvxpkxNnqw" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

#### Wind-Up Protection
To fix this issue, I implemented clamping in the code below using the logic shown in Professor Helbling's slides. The accumulated error is reset to 0 when the controller is clamped, helping to prevent oversaturation.
![](images/Lab6/clamp_slide.jpg)

![](images/Lab6/clamp_code.jpg)


___
## References
I heavily referenced Professor Helbling's slides. I also referenced my own lab 5 page. I discussed ideas with Becky.
