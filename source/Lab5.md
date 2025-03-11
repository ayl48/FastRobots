# Lab 5: Linear PID Control and Linear Interpolation
The objective of this lab

## Prelab
I began by separating my previous lab code for motor control and ToF into header and CPP files to improve code organization. Additionally, I created a new header file specifically for my PID control functions. Due to time constraints, I have not yet created a header file for my IMU code. The header files are shown below.

Motor header file:
![](images/Lab5/motor_header.jpeg)

ToF header file:
![](images/Lab5/ToF_header.jpeg)

![](images/Lab5/PID_header.jpeg)

### Bluetooth Commands
Next, I wrote Bluetooth commands to set parameters and control the start and stop of my PID code from my computer.

The **SET_PID_PARAM** command sets PI parameters (Kp and Ki) and activates a tweak flag, which adjusts the PI calculation. This adjustment is discussed later in the implementation.

Arduino side:
![](images/Lab5/SET_PID_PARAM.jpeg)

Python side:
![](images/Lab5/SET_PID_py.jpeg)

The **SET_CONSTRAINT** allows me to choose PWM value limits to cap speed and prevent the robot from hitting the wall too hard.

Arduino side:
![](images/Lab5/SET_CONSTRAINT.jpeg)

Python side:
![](images/Lab5/SET_CON_py.jpeg)

The **START_RUN** command allows me to set the target distance (setpoint) and PID runtime, then executes PI control for the specified duration. It sends timestamped data, including distances, PWM values, P terms, and I terms, to the computer afterward for plotting.

Arduino side:
![](images/Lab5/START_RUN_ard.jpg)
![](images/Lab5/START_RUN_ard1.jpg)
![](images/Lab5/START_RUN_ard2.jpg)

Python side:
![](images/Lab5/notif.jpeg)

![](images/Lab5/START_RUN_py.jpg)

I also implement a hard stop in the main loop if the Bluetooth connection fails as safety measure.
![](images/Lab5/hard_stop.jpg)

### PI Implementation
The PID equation and block digram from Professor Helbling's slides are shown.
![](images/Lab5/PID_equation.jpeg)

![](images/Lab5/PID_diagram.jpeg)

I only implemented PI control because it was sufficient for reaching my goal of making the robot stop 1ft away from the wall.

## Lab Tasks

### Position Control

#### Proportional (P) Control
First, I implemented proportional control
P control only value:0.04.

PI control without clamping 0.032; 0.01

The distance is measured using the


Three videos of position control working are shown:

Video with P Control:
<iframe width="560" height="315" src="https://www.youtube.com/embed/6NMo0ybRPp8"
    frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture"
    allowfullscreen>
</iframe>

Video with PI Control:
x3

#### Distance Graph
#### P Term Graph
#### I Term Graph

### Extrapolation
Using the same methods from previous labs, I calculated the frequency at which the ToF sensor returns new data: ~9.94 Hz
![](images/Lab5/10Hz.jpeg)

PID speed 121.25 Hz.

Video with old point
Graph with old data point

Video with linear Interpolation
Graph with linear interpolation

###Wind-Up Protection for Integrator
The integrator term in my controller caused a wind-up issue. The accumulated error increased rapidly and could not shrink fast enough as the robot approached the target distance. This issue caused my car to drive directly into the cabinet as shown below.

#### No Wind-up Protection
<iframe width="560" height="315" src="https://www.youtube.com/embed/ITvxpkxNnqw" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

#### Wind-Up Protection
To fix this issue, I implemented clamping in the code below using the logic shown in Professor Helbling's slides. The accumulated error is reset to 0 when the controller is clamped, helping to prevent overshoot.
![](images/Lab5/clamp_slide.jpg)

![](images/Lab5/clamp_code.jpg)

Two videos of my wind-up integrator protection code are shown (Kp=0.05 and Ki = 0.01).
<iframe width="560" height="315" src="https://www.youtube.com/embed/nPRK794NF8k" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

<iframe width="560" height="315" src="https://www.youtube.com/embed/8rM_LcwEsAo" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>


___
## References
I referenced pages written by Nila and Stephen. I also discussed ideas with Sabian, Becky, and Nita.
