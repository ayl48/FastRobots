# Lab 8: Stunts

In this lab, the robot begins on a starting line approximately 1 m away from the wall, moves forward until it is ~1ft from the wall, performs a flip, and then drives back through the starting line.

## Flip Implementation
While linear PID could have been used for this stunt, I opted for open-loop control during the flip to allow the robot to accelerate at full speed and build up the momentum needed to complete the maneuver. Additionally, my robot layout decision from lab 4 placing both batteries at the front of the car provided it with the forward weight distribution that made the open-loop flip effective.

On the Arduino side, I wrote a FLIP command that executes the flip stunt and allows me to adjust the open-loop parameters in each run. After testing, I finalized my open-loop parameters that consistently delivered reliable results, and configured them directly when calling the command from my Python script.

Arduino Code:

Python Code:

![](images/Lab8/distance.jpeg)


## Flip Videos
<iframe width="560" height="315" src="https://www.youtube.com/embed/kqI4PpA5Q6U" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

___
## References
 I referenced Wenyi's page. I discussed ideas with Becky and Akshati.
