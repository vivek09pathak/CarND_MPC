## Writeup Report

---

**MPC Controller Project**

(Note - Hi , The visualization for the simulator on my machine is available at 40 Kmph -https://www.youtube.com/watch?v=v3BWEooDPSo&feature=youtu.be
The visualization for the simulator on my machine is available at 70 Kmph -https://www.youtube.com/watch?v=ZvqyxZ0iwYs&feature=youtu.be)

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

Model Predictive model reframes the task of following a trajectory as an optimization problem as using optimal trajectory.As refrence trajectory where predicted trajectory is used with minimum cost.
As State equations defined is for t+1 time-stamp using t time-stamp state.A cost function defined in the MPC aims to minimize the value of errors attached with trajectory like state and actuators as to penalize the magnitude of input as well as change rate like to remove jerks or sudden change by using cost error it makes the smooth track changes or car doesn't stop.After finding the optimal trajectory will we take the current point and predict our next state and dump the trajectory and repeat the process.

As actuator inputs are controls such as steering angle, acceleration, brake predicting the trajectory and selecting that trajectory which has the least cost as we have applied cost functions on that also.

As update equations used to implement this continuouly predict the vehicle's x and y position, orientation, velocity, cross-track-error and orientation error, and are as follows:

        x_[t+1] = x[t] + v[t] * cos(psi[t]) * dt
        y_[t+1] = y[t] + v[t] * sin(psi[t]) * dt
        psi_[t+1] = psi[t] + v[t] / Lf * delta[t] * dt
        v_[t+1] = v[t] + a[t] * dt
        cte[t+1] = f(x[t]) - y[t] + v[t] * sin(epsi[t]) * dt
        epsi[t+1] = psi[t] - psides[t] + v[t] * delta[t] / Lf * dt

But there was some change in the equations where orientation eqaution is modified as discussed in the classroom because in simultaor positive implies right turn and negative implies left turn.

I have taken mostly eqautions as discussed in the lessons and Q&A where I was stucked for it.

#### 2. Student discusses the reasoning behind the chosen N (timestep length) and dt (elapsed duration between timesteps) values. Additionally the student details the previous values tried.


I started with the value of N=25 and dt=0.1 as discussed in the classroom but since my machine was slow it was taking very much time and was diverting at very angles then I tried with N=20 and dt=0.05 to keep the value of T below 1s as discussed in the classroom as result was good but still the green lines were diverting a lot and then I tried with reference velocity 120 but it gave me too many green lines so I decided with stable model and came with **N=10 and dt=0.1** where it gave me good output as my final values.

But I have made video on both values of N=10 and N=20 please do see both the implementations.

**NOTE- I changed the value of delta roughly from -12 degree to 12 degree.

#### 3. A polynomial is fitted to waypoints. If the student preprocesses waypoints, the vehicle state, and/or actuators prior to the MPC procedure it is described.

As discussed in Q&A project waypoints are pre-processed as shifting the car reference by 90 degree as Homogenous Transformation described in implemetation of particle filter and rotating about the origin.Which easily fit the polynomial easily.


#### 4. The student implements Model Predictive Control that handles a 100 millisecond latency. Student provides details on how they deal with latency.

As per described by my mentor I used the latency from the fist previous timestamp as follows:-

                double latency =0.125;
                px = px + v * cos(psi) * latency;
                py = py + v * sin(psi) * latency;
                psi = psi - v* steer_value/Lf * latency;
                v += v + throttle_value * latency;


Thus accounting for the 100 ms latency.

### Discussion

#### 1. Briefly discuss what could you do to make the implementation more robust?

I tried to implement my model with different parameter to make it work at 100mph but due to slow processors i came upto touching speed of 70 kmp.But i will keep tune the values for cte and orientatiom angle(as I took 3300 for both) where i can tune my model to upto 100mph.
