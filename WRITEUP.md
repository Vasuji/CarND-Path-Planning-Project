# Path Planning Project

## Introduction

The goal of this project is to design and implement a path planner that drives a car in a smooth and safe trajectories on a simulated highway. Simulator sends telemetry info such as the speed and position of the car and sensor fusion information about other cars in the highway. The path planner uses these information and provides a safe, feasible and smooth trajectories which should not break rules such as (1) speed limit 50 mil/s, (2) accelration limit 10 m/s^2 and (3) jerk that should not be greater than 50 m/s^3.

Following are the details of the module:

### Speed and acceleration management. 
To achieve the smooth acceleration up to the allowed maximum with the max speed of 50 mph, at every timestep (0.02 sec), the algorithm increments the reference car speed used for estimating future waypoints by a small amount (0.224 m/s). This leads to the total jerk and acceleration lying below allowed maximum.  When the car’s speed is 49.5 mph or more, the increment of speed stops.
Simultaneously, if sensor fusion detects another car closer than 30 meters in the same lane in front of the ego, the algorithm would start to decrease the reference speed by 0.224 m / s. The speed is decreased until a full stop or until the slower object leaves the lane/increases the distance to more than 30 meters. The C++ spline module has been used to generate the waypoints for the controllers that takes target lane, distance to be covered (based on the reference speed), the reference position of our car as inputs. Staying in the lane is helped by using the frenet coordinates.


### Lane change policies
With the following conditions, the car should try to change lanes when there is a close obstacle in front and in its lane:
1) when the car encounter an obstacle closer than 30 meters ahead in its lane, it would try to change to the left lane if it is present and safe,
2) if not then it consider changing right (same conditions),
3) alternatively is stays in its lane (speed management would be decreasing the speed in this case).
The model continually checks the safety of the adjacent lanes. I used sensor fusion data about location and speed of other vehicles relative to the ego car. If there is a car in the considered lane that meets following conditions the change lane maneuvers was prohibited:
1) within 10 meters in front or behind my car
2) within 30 meters range which might enter 10-meter close zone within the next 3 seconds
On the technical side, I had some issues with sensor fusion frenet data. The s values were not aligned with the ego car’s data, so I had to approximate other car s values for XY coordinates and speed vectors.


### Discussion
The results obtained by running the codes with simulator meet the requirements. However, it can be noticed that there is at least one type of collisions that it cannot avoid – aggressive lane change by another car. In such rear cases, it would make sense to introduce extreme behavior, for example, emergency braking or evasive maneuver, which would violate max acceleration/jerk criteria but still should be a preferred plan of actions.
