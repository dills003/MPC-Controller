**MPC Controller Reflection** 

This is my reflection of the effect each of the MPC Controller parts work and how it went. I aimed for a speed of roughly 30 mph, to match what I did in the PID project.

---

**The Model, Timestep, Polynomial, and Latency**

The MPC Model that I used was taken directly from the quiz. The general flow of the model is:
 1. Define the duration of the trajectory (T). 
    * Duration 'T' is dt * N. The 'T' is how long the model looks into the future. I choose a short future, 0.36 seconds, because my car was moving so slow. Anything longer I discovered caused major proboems. If 'T' is too great, the cost is calculating stuff too far out in the future and is messing with the present. My final parameters were dt=.04s and N = 9. From lecture it stated to stay under two seconds, but that must be for a rapidly moving car without curves.
 2. Define the State
      * I used the same model as shown in the lecture. I used the position of X, position of Y, Psi, Velocity, CTE, and the Error in Psi. X, Y, and Psi were all set to zero after all the waypoints were moved to the car's point of view, with X heading straigh ahead and Y to the left and right of the front of the car. They were allowed to be set to zero, because the car is the origin and pointed directly down the X-axis at all times. All of the state values were calculated as they were in the quiz.
 3. Define the Constraints
      * The acturators in this project were the steering wheel and the accelerator. I played with constraits on the steering, but ended up leaving it a the given 25 degrees of freedom. I contrained it by given the actuator a cost. The accelator I constrained from 0-0.5, because all of the brake light action and jerky accel/decel got to me. I eliminated the brake, because I was going so slow.
 4. Define the Cost Function
      * I used the same cost function as the quiz did. I had the ability to punish CTE, Error in Psi, Velocity Delta, Steering Use, Accelerator Use, Gap between steering angles, Gap between accelerator values. After much trial and error, which was probably the worst night ever, I decided to give the following weights to the costs:
      
      # CTE:     1.5 (Needed it to be a little more important than the velocity costs)
      # EPSI:    2.0 (Seemed like at slower speeds, the position of the front end matter a little bit more than the CTE)
      # VelDiff: 1.0 (If I was building a racecar this would have mattered more, but just keeping the car on the road at about 30 mph, this wasn't a concern)
      # Steer Actuation:     25 (Helped limit steer angle, smooth)
      # Velocity Actuation:   5 (Probably could have been zero, but ratios of Cost is important in MPC)
      # Steer Gap:          500 (Latency and desire for smoothness drove this high of a number)
      # Velocity Gap:         1 (Wasn't concerned about velocity changes)
      

      
 **Conclusion**     
 4. Adjust a parameter with the potential change
 5. Calculate some error
 6. Check if the adjustment of the parameter worked
 7. Adjust the potential changes based on how well the update worked
 8. Repeat from 3.
 
I tried only tuning the P value, tunning all the parameters, adjusting tolerance levels, using curves, straight roads, adjusting speeds, and it was a struggle to get anywhere. I then decided to manually tune, and within 30 minutes, I was finished. I feel like I need to understand how to 'Twiddle' better, so I will revisit it later. My goal was to make it around the track at 30% throttle.

My understanding of the PID parameraters are as follows:
 *'P' - The higher the 'P' the more responsive your plant is, but it comes the penalty of overshoot and oscillation
 
 *'I' - There to decrease Steady State error. The more of it the faster the response but comes with the penalty of overshoot and oscillation.
 
 *'D' - There to put the brakes on the overshooting and oscillation. There to stabilize. It comes with the penalty of stabilization if you use too much of it.
 
Here is how my manual tunning went:

| P      |    I  |  D    | Speed | Observation              |                                                     
|:------:|:-----:|:-----:|:-----:|:-------------------------------------------------------------------:|
10 |	0 |	0	|0.05	| CTE on straight was .019, this is the 'I' term adjustment, it will be small |
10 |	0.1 |	0	| 0.05 |	Weaving out of control, too much 'I' |
10 |	0.01	| 0	| 0.05 |	Weaving better, worse CTE |
10 |	0.001	| 0	| 0.05 |	About the same performance as start |
10 |	0.005 |	0 |	0.05 |	Feel okay about my 'I' term, going to speed up |
10 |	0.005 |	0	| 0.1 |	Too large of corrections, but on road still, speed up and decrease 'P'|
5 |	0.005 |	0	| 0.3 |	Woah, once it is out of shape, all bad, time for brakes |
5	|0.005 |	10	| 0.3	| Still rough, 'P' is too high, too long to recover from monster overshoots, over corrects |
1 |	0.005 |	10 |	0.3	|Almost made it around, too much overshoot |
0.5 |	0.005 |	10	| 0.3	| Makes it all of the way around, pretty close to edge during corrections though |
0.25 |	0.005	| 10 |	0.3 |	Makes it all of the way, little lethargic to respond, fine tuning could be done |


My final values of P: 0.25 , I: 0.005 , and D: 10.0 could be fine tunned to make the driving experience more smooth, but this was as good as my wife's driving so I decided to stop here.
