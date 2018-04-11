Lab 6
=====

## Overview and Motivations - Shannon Hwang

In this lab, methods were implemented that allowed the car to plan and follow a path to a goal given a map of the Stata center basement, the car’s location (using the localization code from the previous lab) and a goal pose. Path planning is an important problem in robotics, and once a path is planned, the ability to follow that path with trajectory tracking is equally critical for a robot to navigate any environment effectively. There are a number of approaches – each with their own pros and cons – for tackling path planning; paths in this lab were planned using a variant of A* . A pure pursuit controller was implemented to track planned trajectories. 


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

### Testing Procedure - Shannon Hwang
Testing in simulation utilized the provided simulation launch file; the robot's performance was evaluated qualitatively through rviz and quantitatively by measuring the average distance between the robot's simulated pose to its correct position on its planned trajectory. Testing with the racecar took place in the Stata basement using real-time sensor input and a safety controller; the robot's performance was evaluated qualitatively through rviz by comparing its position to its visualized planned trajectory. 

#### Simulation

#### Racecar – Shannon Hwang
Testing with the racecar only took place after both trajectory tracking and path planning code had been tested in simulation, so no parameter tuning was necessary during testing.  The pure pursuit controller was tested by following a prescribed loop around the basement. To test other the pure pursuit controller's ability to follow other trajectories as well as the path planner, goal poses were set in rviz and sent to the path planner. The path planner's calculated path to the goal was then displayed in rviz, and the robot used pure pursuit to follow the planned path. The robot's performance was evaluated qualitatively by comparing its trajectories to those seen in rviz.  

### Results

#### Simulation - Akhilan Boopathy
The planner and pure pursuit controller worked as quantitiatively and qualitatively expected in simulation. As shown in figure 4, the planner planned an obstacle free path on a map of the basement of Stata from the current robot pose to the goal pose. Since the path planner took into account the motion constraints of the car, the trajectory was by design sufficiently smooth for the robot to follow. Once the trajectory was found, the robot followed the trajectory qualitatively correctly as seen in figure 4.

##### Pure Pursuit Simulation
<center><img src="assets/images/Lab6SimAll.gif" width="300" ></center>
<center>*Figure 4: A video of the robot planning and following a trajectory in simulation. The goal point is clicked in rviz. The green polygon represents the found trajectory, with an additional line directly between the start and the goal.*</center>

In addition, the robot's simulated position was quantitatively close to the true trajectory, as seen in figure 5. Depending on the initial pose of the robot, the time to converge to the trajectory varied, with greater time needed for convergence for larger initial distances to the trajectory. Once converged, the robot followed the trajectory at an average distance to trajectory of 0.02 m.

##### Pure Pursuit Simulation Error
<center><img src="assets/images/Lab6PurePursuitErrorSim.png" width="300" ></center>
<center>*Figure 5: The distance to the trajectory as a simulated robot followed a trajectory using pure pursuit. The simulated robot starts at varying distances to the trajectory. The steady state error approaches 0.02 m regardless of initial position.*</center>

#### Racecar - Akhilan Boopathy

The real robot planned and followed paths to a specified goal point as qualitatively expected. First, the qualitative performance of the real robot on a predetermined trajectory was evaluated to verify the real life performance of trajectory following. A predefined trajectory around the basement of Stata was used as a target for trajectory following. As shown in the figures below, the robot accurately followed the loop trajectory.

##### Video of Robot Following Loop Trajectory
<center><img src="assets/images/Lab6RealRobotLoop.gif" width="300" ></center>
<center>*Figure 6: A video showing a robot traveling in a loop in the Stata basement. The trajectory it is following is predefined so as to avoid walls.*</center>

##### Visualization of Robot Following Loop Trajectory
<center><img src="assets/images/Lab6LoopRviz.gif" width="300" ></center>
<center>*Figure 7: A video showing a robot in rviz traveling along a predefined trajectory. The red arrows represent the pose estimates of the robot using a particle filter to localize the robot on the map. The blue dot represents the lookahead point on the trajectory that the robot follows. The green line represents the trajectory. Note that in the visualization, the trajectory is slightly offset from the true position.*</center>

The performance of the path planner on the real robot was evaluated next. Once a goal point was manually set, the robot planned a trajectory from the robot's current position to the goal pose using the path planner. The robot then followed the planned trajectory using pure pursuit. In the figures below, the goal pose was set on the other side of an obstacle, so the robot planned and followed a trajectory around the obstacle.

##### Video of Robot Following Planned Trajectory
<center><img src="assets/images/Lab6ColumnReal.gif" width="300" ></center>
<center>*Figure 8: A video showing a robot moving around a column in the Stata basement. The trajectory it is following is planned so as to avoid the obstacle.*</center>

##### Visualization of Robot Following Planned Trajectory
<center><img src="assets/images/Lab6ColumnRviz.gif" width="300" ></center>
<center>*Figure 9: A video showing a robot in rviz planning and traveling along a planned trajectory. The start pose is the robot's current position and the goal pose is manually selected. The red arrows represent the pose estimates of the robot using a particle filter to localize the robot on the map. The blue dot represents the lookahead point on the trajectory that the robot follows. The green line represents the trajectory. Note that in the visualization, the trajectory is slightly offset from the true position.*</center>

## Lessons Learned - Akhilan Boopathy

Completing this lab required both the technical skills to design and implement a trajectory follower and path planner, as well as the communication skills necessary to effectively coordinate with team members.

### Technical Conclusions - Shannon Hwang

This lab provided insight into the theory and implementation of a pure pursuit trajectory tracker and a modified A* path planner. 
Though A* is constrained by the fineness of discretization space in theory, it was worked fine for the RACECAR's needs. 
Theory behind pure pursuit – lookahead distance, nearest distance 
Theory behind circular discretization of A* path planner 
Circular exploration, heuristic calculation
Trajectories need to be refined to account for motion constraints of the car

### CI Conclusions - [Insert Author]

1. Student 1
2. Student 2
3. Student 3
