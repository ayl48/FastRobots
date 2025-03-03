# Lab 4: Motors and Open Loop Control

## Prelab

### Wiring Diagram

![](images/Lab4/lab4_motor_wiring.jpg)

I used pins A0-A3 on the Artemis to control the motors. The Artemis and the motors will be powered by separate batteries because the motors draw large currents, generating noise and inductive spikes when switched on and off.

(Turn this into a table) I used blue wires for the In1 pairs of for both motor drivers and and yellow wires for the In2 pair of the first

## Lab Tasks

### Motor Driver PWM Test
The motor driver circuit's operating voltage is 2.7-10.8VDC according to the [datasheet](https://www.pololu.com/product-info-merged/2130). The battery provided for the car is 3.7 V so I figured that would be a reasonable voltage for the power supply setting.
![](images/Lab4/powersupply.jpg)


I used analogWrite commands to generate PWM signals on the motor driver inputs and scoped the outputs.

![](images/Lab4/pwm_code.jpg)
![](images/Lab4/full_setup.jpg)
![](images/Lab4/connections.jpg)

The output signal has a 3.7V amplitude, matching the power supply. This was consistent across all output pins on both drivers. An example oscilloscope image of one of the outputs is shown.
![](images/Lab4/oscilloscope.jpg)
After verifying that my motor drivers were working  soldered the motor drivers to the battery

### Wheels Spinning On Each Side
The code and the corresponding video for the spin tests on each side of the car are shown.
![](images/Lab4/wheels_code_snippet1.jpg)
<iframe width="560" height="315" src="https://www.youtube.com/embed/gqHCjJhukT0" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>


![](images/Lab4/wheels_code_snippet2.jpg)
<iframe width="560" height="315" src="https://www.youtube.com/embed/sWNrK5JfFV4" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>


### All Wheels Spinning
![](images/Lab4/all_wheels_code.jpg)

<iframe width="560" height="315" src="https://www.youtube.com/embed/O3TMd0nXmHE" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

### Secured Car Components
I secured the components into the car as shown.
![](images/Lab4/Artemis_Top.jpg)

![](images/Lab4/battery_top.jpg)


I used duct tape to create straps that further secured the components by pressing the sticky sides together, leaving the ends adhesive for attachment.
![](images/Lab4/artemis_tape.jpg)

![](images/Lab4/battery_tape.jpg)


### Lower Limit PWM Value to Start Forward Movement


### Calibration

### Open Loop control

### Frequency Discussion for analogWrite

### Lower Limit PWM Value for Motion Maintenance

### References
I referenced Nila's pages for formatting content. I also discussed ideas with Becky.
