Lab 6
=====

## Overview and Motivations - [Insert Author]

Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nam sed consequat ligula. Aliquam erat volutpat. Cras iaculis diam vitae nunc ultricies, et egestas lorem eleifend. Ut sit amet leo vitae libero maximus molestie non ac nunc. Ut ac mi ante. Vivamus convallis convallis neque, sit amet sollicitudin arcu bibendum sit amet. Phasellus finibus dolor vitae leo cursus, eu lobortis nisl blandit. Quisque tincidunt et nisi a hendrerit. Sed et nunc quis neque egestas sollicitudin. Curabitur auctor bibendum odio. Proin aliquam cursus metus, at fermentum tellus luctus vel. Morbi ut mi id augue lacinia faucibus.

## Proposed Approach - [Insert Author]

Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nam sed consequat ligula. Aliquam erat volutpat. Cras iaculis diam vitae nunc ultricies, et egestas lorem eleifend. Ut sit amet leo vitae libero maximus molestie non ac nunc. Ut ac mi ante. Vivamus convallis convallis neque, sit amet sollicitudin arcu bibendum sit amet. Phasellus finibus dolor vitae leo cursus, eu lobortis nisl blandit. Quisque tincidunt et nisi a hendrerit. Sed et nunc quis neque egestas sollicitudin. Curabitur auctor bibendum odio. Proin aliquam cursus metus, at fermentum tellus luctus vel. Morbi ut mi id augue lacinia faucibus.

### Initial Setup - [Insert Author]

### Technical Approach - [Insert Author]

#### Trajectory Follower

##### Pure Pursuit Equation
<center><img src="assets/images/Lab6PurePursuitEqn.JPG" width="300" ></center>
<center>*Equation 1: The equation for determining the steering angle via pure pursuit given a goal point (x,y) relative to the robot's current pose, where L is the distance between axles on the robot.*</center>

##### Pure Pursuit Simulation
<center><img src="assets/images/Lab6SimPurePursuit.gif" width="300" ></center>
<center>*Figure 1: A diagram showing a simulated car following a lookahead point via pure pursuit.*</center>

#### Path Planner

##### Circle Space Search
<center><img src="assets/images/Lab6CircleSpace.JPG" width="300" ></center>
<center>*Figure 2: A diagram illustrating A* search through circle space. Circles are found in the space exploration step, then are used as part of the heuristic for heuristic search. Image from https://mediatum.ub.tum.de/doc/1283837/826052.pdf.*</center>

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

#### Simulation

##### Pure Pursuit Simulation
<center><img src="assets/images/Lab6PureSimAll.gif" width="300" ></center>
<center>*Figure 4: The robot planning and following a trajectory in simulation. The goal point is clicked in rviz.*</center>

##### Pure Pursuit Simulation Error
<center><img src="assets/images/Lab6PurePursuitErrorSim.png" width="300" ></center>
<center>*Figure 5: The distance to the trajectory as a simulated robot followed a trajectory using pure pursuit. The steady state error approaches 0.02 m.*</center>


#### Real Roboot

## Lessons Learned - [Insert Author]

Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nam sed consequat ligula. Aliquam erat volutpat. Cras iaculis diam vitae nunc ultricies, et egestas lorem eleifend. Ut sit amet leo vitae libero maximus molestie non ac nunc. Ut ac mi ante. Vivamus convallis convallis neque, sit amet sollicitudin arcu bibendum sit amet. Phasellus finibus dolor vitae leo cursus, eu lobortis nisl blandit. Quisque tincidunt et nisi a hendrerit. Sed et nunc quis neque egestas sollicitudin. Curabitur auctor bibendum odio. Proin aliquam cursus metus, at fermentum tellus luctus vel. Morbi ut mi id augue lacinia faucibus.

### Technical Conclusions - [Insert Author]

Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nam sed consequat ligula. Aliquam erat volutpat. Cras iaculis diam vitae nunc ultricies, et egestas lorem eleifend. Ut sit amet leo vitae libero maximus molestie non ac nunc. Ut ac mi ante. Vivamus convallis convallis neque, sit amet sollicitudin arcu bibendum sit amet. Phasellus finibus dolor vitae leo cursus, eu lobortis nisl blandit. Quisque tincidunt et nisi a hendrerit. Sed et nunc quis neque egestas sollicitudin. Curabitur auctor bibendum odio. Proin aliquam cursus metus, at fermentum tellus luctus vel. Morbi ut mi id augue lacinia faucibus.

Duis vel nunc sit amet risus consectetur dictum. Nulla mollis varius erat, vitae gravida est elementum a. Curabitur velit sapien, placerat ac scelerisque quis, ultricies at sem. Maecenas ut elit congue, condimentum lacus eu, scelerisque nunc. Curabitur mattis velit vitae sem placerat varius vel euismod leo. Cras quis elit quam. Proin scelerisque lobortis erat, eu euismod ex mattis ac. Curabitur non felis mauris. Integer mauris nisi, rutrum id finibus vel, ornare quis diam. Cras lectus nisi, pharetra ut elit at, porta auctor ex. Cras lobortis nisl leo, varius aliquet arcu sollicitudin vel. Aliquam quis nulla sapien. Donec porttitor, tortor vel iaculis vehicula, ipsum eros dictum eros, sit amet tempor orci felis vitae magna. Sed velit lacus, tincidunt sit amet quam sed, aliquam porttitor ex.

### CI Conclusions - [Insert Author]

1. Student 1
2. Student 2
3. Student 3
