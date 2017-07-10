**MPC Controller Reflection** 

This is my reflection of the effect each of the MPC Controller parts work and how it went. I aimed for a speed of roughly 30 mph, to match what I did in the PID project. The first couple of attempts at this assignment failed, as I did not understand how to incorporate latency into my program. Thank you very much to the Udacity Reviewer that pointed me in the correct direction. Tuning the car was a nightmare before the help, it was a miracle that is stayed on the road. With predicting the future states, everything behaves so much better.

---

**The Model, Time Step, Polynomial, and Latency**

The MPC Model that I used was taken directly from the quiz. The general flow of the model is:
 1. Define the duration of the trajectory (T). 
    * Duration 'T' is dt * N. The 'T' is how long the model looks into the future. I choose a short future, 0.36 seconds, because my car was moving so slow. Anything longer I discovered caused major problems. If 'T' is too great, the cost is calculating stuff too far out in the future and is messing with the present. My final parameters were dt=.04s and N = 9. From lecture it stated to stay under two seconds, but that must be for a rapidly moving car without curves.
 2. Define the State
      * I used the same model as shown in the lecture. I used the position of X, position of Y, Psi, Velocity, CTE, and the Error in Psi. X, Y, and Psi were all set to zero after all the waypoints were moved to the car's point of view, with X heading straight ahead and Y to the left and right of the front of the car. They were allowed to be set to zero, because the car is the origin and pointed directly down the X-axis at all times. All of the state values were calculated as they were in the quiz.
 3. Define the Constraints
      * The actuators in this project were the steering wheel and the accelerator. I played with constraints on the steering, but ended up leaving it at the given +/-25 degrees of travel. I constrained it by given the actuator a cost. The accelerator I constrained from 0-0.5, because all of the brake light action and jerky accel/decel got to me. I eliminated the brake, because I was going so slowly.
 4. Define the Cost Function
      * I used the same cost function as the quiz did. I had the ability to punish CTE, Error in Psi, Velocity Delta, Steering Use, Accelerator Use, Gap between steering angles, Gap between accelerator values. After much trial and error, which was probably the worst night ever, I decided to give the following weights to the costs:

      
        * CTE:     2.0 (Needed it to be a little more important than the velocity costs)
        * EPSI:    2.0 (Match importance of CTE)
        * VelDiff: 1.0 (If I was building a racecar this would have mattered more, but just keeping the car on the road at about 30 mph, this wasn't a concern)
        * Steer Actuation:     25 (Helped limit steer angle, smoothing)
        * Velocity Actuation:   5 (Probably could have been zero, but ratios of Cost is important in MPC)
        * Steer Gap:          500 (Latency and desire for smoothness drove this high of a number)
        * Velocity Gap:         1 (Wasn't concerned about velocity changes)
      
5. The Flow
     1. Get the current vehicle map position and waypoints
     2. Rotate everything
     3. Find the best third order polynomial to fit the waypoints
     4. Calculate the CTE and Error Psi
     5. Give the Solver the state and polynomial coefficients
     6. The solver takes the State, Model, Constraints, and Cost Function, crunches the numbers and outputs the best actuations for the steering angle and accelerator.
     7. Repeat
6. Polynomial Fitted to Waypoints
     * The values that input into the program have x, y, and psi in terms of a global map. We need to put them in terms of the car, because we are pretending that the car is the organ with the x-axis pointing straight forward. Therefore, for each waypoint, I subtracted its x,y value from the car's global x,y value (to get it in terms of the car). I tried the standard 2D rotation matrix and couldn't get the car to behave. I then, which should be required, decided to draw the 'Yellow Next Value' line and was surprised to see that the standard rotation matrix didn't work. I then went to my Robotics text and modified the 3D Y rotation into 2D. Magically the line appeared down the center of the car and lane (when I had reference velocity set to zero). For each waypoint I first subtracted the x,y values from the cars and then put it through my rotation matrix. After all of that was done, I ran the waypoints through the provided polyeval function. I choose to use third order, because we are going around curved and third order usually does the trick.
7. Latency
     * In my first attempt to deal with latency, I set dt to 0.1 seconds. This caused an endless mountain of issues for me. I couldn't set N to a low enough value to help with my slow speed. Therefore, to handle latency, I gave a really large cost to the steering gap and steer actuation. I could have done the same if velocity was a concern of mine. I figured with the higher costs, we wouldn't be trying to actuate unless it was truly necessary. 
     All of the above was incorrect. I was not predicting the state of the vehicle 100ms in the future. The Udacity Reviewer helped me realize that actuators will reach the simulator with a 100ms second delay. I needed to make a good guess at where the car would be at the end of this delay. The code for this is in Main.cpp lines 167-175. Thanks again to whomever the Reviewer was that helped.
