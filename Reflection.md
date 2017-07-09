**PID Controller Reflection** 

This is my reflection of the effect each of the PID components had in my implementation and how the final hyperparameters were chosen for Udacity's PID Controller Project.

---

**How it went**

My original plan was to use 'Twiddle' to automatically find all of my PID values. After implementing 'Twiddle' in the quiz, I thought it was one of the most amazing, easy-to-use tools that I have ever seen. Gone would be the days of PID guess and check. After about two days of trying to tune 'Twiddle', I gave up. This can be seen on lines 67-200 or the main.cpp file. I believe how I implemented or maybe my understanding of it is incorrect. My understandin of 'Twiddle' is the following:
 1. Initialize the parameters
 2. Initialize potential changes to the parameters
 3. Calculate some error
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
