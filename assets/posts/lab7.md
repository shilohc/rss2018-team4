Lab 6
=====

## Overview and Motivations - [Insert Author]

Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nam sed consequat ligula. Aliquam erat volutpat. Cras iaculis diam vitae nunc ultricies, et egestas lorem eleifend. Ut sit amet leo vitae libero maximus molestie non ac nunc. Ut ac mi ante. Vivamus convallis convallis neque, sit amet sollicitudin arcu bibendum sit amet. Phasellus finibus dolor vitae leo cursus, eu lobortis nisl blandit. Quisque tincidunt et nisi a hendrerit. Sed et nunc quis neque egestas sollicitudin. Curabitur auctor bibendum odio. Proin aliquam cursus metus, at fermentum tellus luctus vel. Morbi ut mi id augue lacinia faucibus.

## Proposed Approach - [Insert Author]

Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nam sed consequat ligula. Aliquam erat volutpat. Cras iaculis diam vitae nunc ultricies, et egestas lorem eleifend. Ut sit amet leo vitae libero maximus molestie non ac nunc. Ut ac mi ante. Vivamus convallis convallis neque, sit amet sollicitudin arcu bibendum sit amet. Phasellus finibus dolor vitae leo cursus, eu lobortis nisl blandit. Quisque tincidunt et nisi a hendrerit. Sed et nunc quis neque egestas sollicitudin. Curabitur auctor bibendum odio. Proin aliquam cursus metus, at fermentum tellus luctus vel. Morbi ut mi id augue lacinia faucibus.

### Initial Setup - [Insert Author]

### Technical Approach - [Insert Author]

#### Trajectory Follower
The robot followed trajectories provided to it by pure pursuit of a lookahead point on the trajectory. The lookahead point was found by locating the point on the trajectory closest to it. Starting from this point, the next point on the trajectory that is a specified lookahead distance away from the robot is picked to be the lookahead point. This is done to ensure that the robot follows the trajectory rather than just reaching it. Next, to move the robot towards the lookahead point, pure pursuit control is applied to the robot. The speed of the robot is set to a constant, and the robot's steering angle is calculated with equation 1 using the lookahead point as an input. Before applying equation 1, the lookahead point is converted to be relative to the robot's current pose. 

##### Pure Pursuit Steering Angle Equation
<center><img src="assets/images/Lab6PurePursuitEqn.JPG" width="300" ></center>
<center>*Equation 1: The equation for determining the steering angle via pure pursuit given a goal point (x,y) relative to the robot's current pose, where L is the distance between axles on the robot.*</center>

As the lookahead point changes, the robot moves continuously towards the current lookahead point as illustrated in figure 1. By selecting lookahead points along a trajectory that are sufficiently ahead of the robot's current position, the robot can be made to follow the trajectory.

##### Pure Pursuit Illustration
<center><img src="assets/images/Lab6SimPurePursuit.gif" width="300" ></center>
<center>*Figure 1: A video showing a simulated car following a lookahead point via pure pursuit.*</center>

#### Path Planner

##### Circle Space Search
<center><img src="assets/images/Lab6CircleSpace.JPG" width="300" ></center>
<center>*Figure 2: A diagram illustrating A star search through circle space. Circles are found in the space exploration step, then are used as part of the heuristic for heuristic search. Image from https://mediatum.ub.tum.de/doc/1283837/826052.pdf.*</center>

##### Rough vs. Refined Trajectories
<center><img src="assets/images/Lab6RoughRefined.png" width="300" ></center>
<center>*Figure 3: Length of rough trajectories versus refined trajectories. After trajectories are refined to account for car's motion constraints, they become longer.*</center>

### ROS Implementation - [Insert Author]

#### Trajectory Follower

#### Path Planner

## Experimental Evaluation - [Insert Author]

Nulla tempus tempor sollicitudin. Sed id tortor vestibulum, tincidunt lorem a, suscipit lacus. Mauris vitae pretium libero, at dapibus massa. Curabitur eleifend bibendum pharetra. Nullam gravida viverra lacus eu blandit. Praesent nec odio ut magna scelerisque vulputate. Sed in libero porta, imperdiet magna maximus, efficitur urna.

### Testing Procedure - [Insert Author]

#### Simulation

#### Real Robot

### Results - [Insert Author]

#### Simulation - Akhilan Boopathy
The planner and pure pursuit controller worked as quantitiatively and qualitatively expected in simulation. As shown in figure 4, the planner planned an obstacle free path on a map of the basement of Stata from the current robot pose to the goal pose. Since the path planner took into account the motion constraints of the car, the trajectory was by design sufficiently smooth for the robot to follow. Once the trajectory was found, the robot followed the trajectory qualitatively correctly as seen in figure 4.

##### Pure Pursuit Simulation
<center><img src="assets/images/Lab6PureSimAll.gif" width="300" ></center>
<center>*Figure 4: A video of the robot planning and following a trajectory in simulation. The goal point is clicked in rviz. The green polygon represents the found trajectory, with an additional line directly between the start and the goal.*</center>

In addition, the robot's simulated position was quantitatively close to the true trajectory, as seen in figure 5. Depending on the initial pose of the robot, the time converge to the trajectory varied. Once converged, the robot followed the trajectory at an average error of 0.02 m.

##### Pure Pursuit Simulation Error
<center><img src="assets/images/Lab6PurePursuitErrorSim.png" width="300" ></center>
<center>*Figure 5: The distance to the trajectory as a simulated robot followed a trajectory using pure pursuit. The simulated robot starts at varying distances to the trajectory. The steady state error approaches 0.02 m regardless of initial possition.*</center>

#### Real Robot

The real robot planned and followed paths to a specified goal point as qualitatively expected. Once a goal point was manually set, the robot planned a trajectory from the robot's current position to the goal pose using the path planner. Next, the robot followed the planned trajectory using pure pursuit.

#### Two Corners Real Robot
<video src="two corners rl.mp4" width="320" height="200" controls preload></video>

## Lessons Learned - Akhilan Boopathy

Completing this lab required both the technical skills to design and implement a trajectory follower and path planner, as well as the communication skills necessary to effectively coordinate with team members.

### Technical Conclusions - [Insert Author]

Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nam sed consequat ligula. Aliquam erat volutpat. Cras iaculis diam vitae nunc ultricies, et egestas lorem eleifend. Ut sit amet leo vitae libero maximus molestie non ac nunc. Ut ac mi ante. Vivamus convallis convallis neque, sit amet sollicitudin arcu bibendum sit amet. Phasellus finibus dolor vitae leo cursus, eu lobortis nisl blandit. Quisque tincidunt et nisi a hendrerit. Sed et nunc quis neque egestas sollicitudin. Curabitur auctor bibendum odio. Proin aliquam cursus metus, at fermentum tellus luctus vel. Morbi ut mi id augue lacinia faucibus.

Duis vel nunc sit amet risus consectetur dictum. Nulla mollis varius erat, vitae gravida est elementum a. Curabitur velit sapien, placerat ac scelerisque quis, ultricies at sem. Maecenas ut elit congue, condimentum lacus eu, scelerisque nunc. Curabitur mattis velit vitae sem placerat varius vel euismod leo. Cras quis elit quam. Proin scelerisque lobortis erat, eu euismod ex mattis ac. Curabitur non felis mauris. Integer mauris nisi, rutrum id finibus vel, ornare quis diam. Cras lectus nisi, pharetra ut elit at, porta auctor ex. Cras lobortis nisl leo, varius aliquet arcu sollicitudin vel. Aliquam quis nulla sapien. Donec porttitor, tortor vel iaculis vehicula, ipsum eros dictum eros, sit amet tempor orci felis vitae magna. Sed velit lacus, tincidunt sit amet quam sed, aliquam porttitor ex.

### CI Conclusions - [Insert Author]

1. Student 1
2. Student 2
3. Student 3
