# Lab 12: Path Planning and Execution


## Method
I used Arduino code from the previous labs and called my commands from Python to complete this lab. I only used three Arduino commands in my Python code: LINEAR_PID, FORWARD, and START_ORIENTATION_PID. I did not do any localization

Initially, I attempted to use my own robot, but I discovered that both of my time-of-flight sensors had stopped working—likely due to damage from the impact of my flip stunts. Code that had previously worked was now returning either zero or completely nonsensical distance values. The day after the ECE Robotics showcase, where I demonstrated my stunt, the code printed a message in serial indicating that there are wiring issues with both sensors. I did not have enough time to resolder my sensors so I opted to use Becky’s robot instead.


![](images/Lab12/sim.jpeg)

## Implementation


### Path Execution
<iframe width="560" height="315" src="https://www.youtube.com/embed/EpykFcLn-yE" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

___
## References
I partnered with Becky on this lab and discussed ideas with Akshati.
