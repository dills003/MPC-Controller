**MPC Controller Reflection** 

This is my reflection of the effect each of the MPC Controller parts work and how it went. I aimed for a speed of roughly 30 mph, to match what I did in the PID project.

---

**The Model, Timestep, Polynomial, and Latency**

The MPC Model that I used was taken directly from the quiz. The general flow of the model is:
 1. Define the duration of the trajectory (T). 
    * Duration 'T' is dt * N. The 'T' is how long the model looks into the future. I choose a short future, 0.36 seconds, because my car was moving so slow. Anything longer I discovered caused major problems. If 'T' is too great, the cost is calculating stuff too far out in the future and is messing with the present. My final parameters were dt=.04s and N = 9. From lecture it stated to stay under two seconds, but that must be for a rapidly moving car without curves.
 2. Define the State
      * I used the same model as shown in the lecture. I used the position of X, position of Y, Psi, Velocity, CTE, and the Error in Psi. X, Y, and Psi were all set to zero after all the waypoints were moved to the car's point of view, with X heading straight ahead and Y to the left and right of the front of the car. They were allowed to be set to zero, because the car is the origin and pointed directly down the X-axis at all times. All of the state values were calculated as they were in the quiz.
 3. Define the Constraints
      * The actuators in this project were the steering wheel and the accelerator. I played with constraints on the steering, but ended up leaving it at the given +/-25 degrees of travel. I constrained it by given the actuator a cost. The accelerator I constrained from 0-0.5, because all of the brake light action and jerky accel/decel got to me. I eliminated the brake, because I was going so slowly.
 4. Define the Cost Function
      * I used the same cost function as the quiz did. I had the ability to punish CTE, Error in Psi, Velocity Delta, Steering Use, Accelerator Use, Gap between steering angles, Gap between accelerator values. After much trial and error, which was probably the worst night ever, I decided to give the following weights to the costs:

      
        * CTE:     1.5 (Needed it to be a little more important than the velocity costs)
        * EPSI:    2.0 (Seemed like at slower speeds, the position of the front end matter a little bit more than the CTE)
        * VelDiff: 1.0 (If I was building a racecar this would have mattered more, but just keeping the car on the road at about 30 mph, this wasn't a concern)
        * Steer Actuation:     25 (Helped limit steer angle, smooth)
        * Velocity Actuation:   5 (Probably could have been zero, but ratios of Cost is important in MPC)
        * Steer Gap:          500 (Latency and desire for smoothness drove this high of a number)
        * Velocity Gap:         1 (Wasn't concerned about velocity changes)
      
5. The Flow
     1. Get the current vehicle map position and waypoints
     2. Rotate everything
     3. Find the best third order polynomial to fit the waypoints
     4. Calculate the CTE and Error Psi
     5. Give the Solver the state and polynomial coefficents
     6. The solver takes the State, Model, Constraints, and Cost Function, crunches the numbers and outputs the best actuations for the steering angle and acclerator.
     7. Repeat
6. Latency
     * In my first attempt to deal with latency, I set dt to 0.1 seconds. This caused an endles mountain of issues for me. I couldn't set N to a low enough value to help with my slow speed. Therefore, to handle latency, I gave a really large cost to the steering gap and steer actuation. I could have done the same if velocity was a concern of mine. I figured with the higher costs, we wouldn't be trying to actuate unless it was truly necessary. Also, when considering the actuation predictions, I use the previous time step after the first pass to set delta0 and a0.
      
