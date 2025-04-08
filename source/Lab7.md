# Lab 7: Kalman Filter

## Drag and Momentum Estimation

I use a step response to estimate drag and momentum by initially setting the PWM to 0 and then applying a constant PWM value. I chose a PWM of 100, which is about 39% of the maximum PWM used in my linear PID lab, instead of the recommended 50-100% of the maximum u, because the robot moved too quickly at higher PWM values, making it difficult to collect a sufficient number of data points.

![](images/Lab7/100pwm.jpeg)

The distance and velocity graphs are shown below. In the velocity graph, you can see that the velocity settles on a value of ~1700 mm/s at time ~2.12s. I estimate the 90% rise time to be ~1.91s. The speed at 90% rise time is ~1666 mm/s. This estimate is based on my fitted velocity graph, though the fit isn't perfect and doesn't accurately capture the settling at the end. I had difficulty adjusting the fitted curve so I have attached my best attempt.
![](images/Lab7/distance.jpeg)

![](images/Lab7/velocity.jpeg)

![](images/Lab7/fitted_velocity.jpeg)

The following equations can be used to calculate drag and momentum.
![](images/Lab7/drag_eq.png)

For ease of calculation, I converted my values to SI units before performing the calculations.
![](images/Lab7/calc.png)


## Kalman Filter Implementation in Python (Simulation)
![](images/Lab7/kf_py.jpeg)
![](images/Lab7/kf_py2.jpeg)

The estimated model parameters done using calculations is decently accurate, but does not follow the data too closely. Here, sigma1=sigma2=70 and sigma3=15.

![](images/Lab7/model_est.jpeg)

Putting more trust in our model, the Kalman graph deviates from my measurements too much. Here, sigma1= sigma2=50 and sigma3=50.

![](images/Lab7/kf_model_py.jpeg)

Putting more trust in our measurements, the Kalman graph matches the measurements almost exactly. Here, sigma1=sigma2=180 and sigma3=45.

![](images/Lab7/kf_measure_py.jpeg)


## Kalman Filter Implementation on the Robot
I implemented the following Kalman Filter algorithm provided in Professor Helbling's slides.
![](images/Lab7/kalman_math.jpeg)

I organized my Kalman filter code into kf.cpp and chose not to include the kf.h file since it only contains a single function: kalman_filter. This function takes in mu (the control input, e.g., PWM), y (the latest sensor measurement), and an update flag, which is set to 0 or 1 in my KF command that will be discussed later. The function always predicts and prepares a measurement update every time it is called. However, it only updates the state estimate x and state uncertainty sig when the update flag is set to 1.

![](images/Lab7/kf_ard1.jpeg)
![](images/Lab7/kf_ard2.jpeg)

The KF command incorporates the Kalman filter into the Lab 5 linear extrapolation PID framework. I call the kalman_filter function with the update flag set to 1 when time-of-flight distance data is available, and set it to 0 when data is not yet ready. This ensures that the prediction step continues to run consistently, even when no new measurements are received. The prediction step will use the previous data if new measurements are unavailable. Updates are only applied once the robot receives its first valid distance measurement.

![](images/Lab7/kf_case1.jpeg)
![](images/Lab7/kf_case2.jpeg)

![](images/Lab7/kf_graph.png)
![](images/Lab7/PWM.png)
![](images/Lab7/P_val.png)
![](images/Lab7/I_val.png)

<iframe width="560" height="315" src="https://www.youtube.com/embed/tTEcGPCQIAs" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

___

## References
I referenced Professor Helbling's slides and Wenyi's page. I also discussed ideas with Becky, Sabian, and Akshati.
