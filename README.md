## Writeup Report

---

**MPC Controller Project**

(Note - Hi , The visualization for the simulator on my machine is available at - https://youtu.be/0fdTAO2y_w4)

The broad goals / steps of this project are the following:

* Set N and dt.
* Fit the polynomial to the waypoints.
* Calculate initial cross track error and orientation error values.
* Define the components of the cost function (state, actuators, etc).
* Define the model constraints.
* Calculate steering angle and throttle using MPC.


## [Rubric](https://review.udacity.com/#!/rubrics/896/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Vehicle-Detection/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

### MPC Controller

#### 1. Student describes their model in detail. This includes the state, actuators and update equations.

The MPC controller converts the problem of predicting the correct trajectory for the robot into an optimization problem as David summarises in the classroom. A 'Cost function' is defined in an MPC Controller and the aim is to minimize the value of 'Cost' associated with following a particular trajectory. Various actuator inputs are simulated, where actuators are nothing but controls such as steering angle, acceleration, brake(deacceleration) etc , predicting the trajectory and selecting that trajectory which has the least cost. Once this 'optimal' trajectory is determined , only the first few actuator commands are executed and the remaining trajectory is not used. The idea being, instead of using the predicted trajectory , use the new current state and repeat the prediction process for this new state. Thus, MPC constantly re-evaluates the inputs to find the optimal trajectory.

The equations used to implement this continuous re-evaluation use the vehicle's x and y position, orientation, velocity, cross-track-error and orientation error, and are as follows:

![alt text](https://github.com/arpitsri3/carND_P10/blob/master/Equations.png)

An additional point I'd like to mention is that the equations for orientation and orientation error had to be modified with a negative sign. Because as discussed in the classroom, in the simulator , a positive value implies a right turn and a negative value implies a left turn.

I have mostly followed the instructions given in the project Q&A video linked in the project module, whenever I got stuck about how to implememt a particular equation and most of the code is adapted and taken from the MPC - 'Mind the Line' quiz and the project Q&A session.

#### 2. Student discusses the reasoning behind the chosen N (timestep length) and dt (elapsed duration between timesteps) values. Additionally the student details the previous values tried.


The value chosen for N and dt ultimately used are 10 and 0.1. These were the values I started with after going through the project Q&A video , however I tried two other combinations-
1. N-25, dt-0.05 - I picked this value from 'Mind the Line' quiz in the classroom but it made the processing slow (as is expected) (also I don't have very good hardware), so ultimately I have not used it.
2. N-20, dt-0.1 

I think , and also as explained in the Q&A , 10 and 0.1 just work because the timestep is not so large that it slows down the MPC neither is it so small that it doesn't give a good prediction. I guess it is just a very optimal combination for the problem. Technically speaking the MPC considers a 1 second duration for correcting trajectory.

#### 3. A polynomial is fitted to waypoints. If the student preprocesses waypoints, the vehicle state, and/or actuators prior to the MPC procedure it is described.

As discussed in the project Q&A the waypoints are pre-processed by shifting car reference model 90 degrees and rotating about the origin. This effectively makes it easy for fitting the polynomial.


#### 4. The student implements Model Predictive Control that handles a 100 millisecond latency. Student provides details on how they deal with latency.

Again as per the hint given in the project Q&A the same actuations are modified with the condition:

if (t > 1) {
        a0 = vars[a_start + t - 2];
        delta0 = vars[delta_start + t - 2];
      }

Thus, after the first timestep, the actuator values used, are the ones for the previous to previous timestep, thus accounting for the 100 ms latency.

### Discussion

#### 1. Briefly discuss what could you do to make the implementation more robust?

I tried a number of times to make the vehicle simulation at a reference speed of 100. But my implementaion always failed at the point after the bridge. I would continue to tune the weights for CTE and orientation angle error (which are currently at 3333) to see if i can make it work at 100.
