Lab 6
=====

## Overview and Motivations - Shannon Hwang

In this lab, methods were implemented that allowed the car to plan and follow a path to a goal given a map of the Stata center basement, the car’s location (using the localization code from the previous lab) and a goal pose. Path planning is an important problem in robotics, and once a path is planned, the ability to follow that path with trajectory tracking is equally critical for a robot to navigate any environment effectively. There are a number of approaches – each with their own pros and cons – for tackling path planning; paths in this lab were planned using a variant of A* . A pure pursuit controller was implemented to track planned trajectories. 


## Proposed Approach - Shiloh Curtis

As in previous labs, the team met to discuss the lab and split the major code components among the team members.  Since design of the path planner depended on the trajectory format used by the trajectory follower, these two components were developed mostly in sequence: the whole team split up to complete the trajectory follower by an early intermediate deadline, then moved on to the path planner.  The path planner was tested in simulation first; once it produced paths that the trajectory follower could easily follow, it was run on the real robot.  

### Initial Setup - Shiloh Curtis

The trajectory follower was designed and implemented first, so the path planner could be designed against the same trajectory format and tested by checking whether the trajectory follower could follow its paths; therefore, the trajectory follower was split among all team members, with an early deadline for finishing a first implementation pass at pure pursuit.  It was then debugged by a subset of the team while the other team members began work on the path planner.  

The path planner was split among the remaining team members.  It was initially tested in simulation, first by visualizing the resulting trajectory and checking that it matched the expected trajectory, then using the trajectory follower.  

Once the path planner and trajectory follower worked together in simulation, testing began on the robot.  The trajectory follower was tested both alone, using a pre-recorded trajectory of the basement loop, and in combination with the path planner.  

### Technical Approach

#### Trajectory Follower - Akhilan Boopathy
The robot followed trajectories provided to it by pure pursuit of a lookahead point on the trajectory. The lookahead point was found by first locating the point on the trajectory closest to it. Starting from this point, the next point on the trajectory a specified lookahead distance away from the robot was picked to be the lookahead point. This was done to ensure that the robot followed the trajectory rather than reaching it and stopping. Next, to move the robot towards the lookahead point, pure pursuit control was applied to the robot. The speed of the robot was set to a constant, and the robot's steering angle was calculated with equation 1 using the lookahead point as an input. Before applying equation 1, the lookahead point was transformed to be relative to the robot's current pose. 

##### Pure Pursuit Steering Angle Equation
<center><img src="assets/images/Lab6PurePursuitEqn.JPG" width="300" ></center>
<center>*Equation 1: The equation for determining the steering angle via pure pursuit given a goal point (x,y) relative to the robot's current pose, where L is the distance between axles on the robot.*</center>

As the lookahead point changed, the robot moved continuously towards the current lookahead point as illustrated in figure 1. When the lookahead point moved around a corner, the robot followed it. By selecting lookahead points along a trajectory that are sufficiently ahead of the robot's current position, the robot can be made to follow the trajectory.

##### Pure Pursuit Illustration
<center><img src="assets/images/Lab6SimPurePursuit.gif" width="300" ></center>
<center>*Figure 1: A video showing a simulated car following a lookahead point via pure pursuit. The blue dot represents the moving lookahead point.*</center>

#### Path Planner - Tony Zhang
We used two levels of A\* search algorithm for path planning. A\* is an informed search algorithm which checks the available state space for all possible paths to goal and chooses the path that provides the minimal cost. In A\*, cost is calculated by summing the cost to move in that direction plus the estimated cost of the path between the current node and goal.

The implementation of A\* relies on python’s Priority Queue structure to be able to order nodes based on their priority (or estimated cost). While there remain unexplored nodes, the algorithm iterates at looking at the neighbors of the lowest priority node, which is the node with the lowest cost path at the moment. The algorithm either updates the node’s costs and parent node if it has been seen before and is cheaper using this path. Otherwise, the algorithm will add the node and its current parent and cost to the set of explored nodes. This iteration continues until the goal state has been seen. Once the goal state is reached, the algorithm backtracks through the search tree and determines the path by looking at each node parent, which is stored and updated within the algorithm’s loop. 

For the algorithm to function properly, it requires accurate movement cost estimations and heuristic cost estimations to goal. To find the shortest solution, the heuristic must be admissible: never overestimates of the cost of the path to the goal node. Our algorithm initially implements two different options for heuristics.

In addition to the heuristic calculation, the other component of the priority calculation is the cost of moving to the next node. We weight the cost based on whether the next node is in the direction of the goal or not. We determined this cost by comparing the angle between vectors (current node, next node) and (current node, goal) and the angle between vectors (current node, next node) and (current node, start). If the angle to goal is less than or equal to the angle to start, then that next node is moving in the correct direction and is given a low weighted cost. Otherwise, the cost of movement is weighted extremely high such that this potential path will be lower in the priority queue. In the experimental evaluation section, we discuss how the optimal heuristic values and cost values were determined for A\* path planning.

In addition to a rough trajectory produced by the first level of A\* searching, we added a second level of A\* searching to smooth the trajetory, which takes in additional cost and heuristic to adjust the trajectory and make it smoother. This will allow the car to move faster and turn more smootherly.

##### Circle Space Search
<center><img src="assets/images/Lab6CircleSpace.JPG" width="300" ></center>
<center>*Figure 2: A diagram illustrating A star search through circle space. Circles are found in the space exploration step, then are used as part of the heuristic for heuristic search. Image from https://mediatum.ub.tum.de/doc/1283837/826052.pdf.*</center>

##### Rough vs. Refined Trajectories
<center><img src="assets/images/Lab6RoughRefined.png" width="300" ></center>
<center>*Figure 3: Length of rough trajectories versus refined trajectories. After trajectories are refined to account for car's motion constraints, they become longer.*</center>

### ROS Implementation - Shiloh Curtis, Tony Zhang

#### Trajectory Follower - Shiloh Curtis

Trajectories are represented piecewise as a list of (x, y) points.  This representation was chosen due to its simplicity and ease of use.  

The trajectory follower is implemented as a single ROS node, which subscribes to `/vesc/odom` for odometry data and `/trajectory/current` for the trajectory to follow and publishes driving commands.  Each time the odometry callback is called, the steering angle is recalculated and applied based on the new odometry data, and the latest lookahead point is visualized.  (For convenience, topic names, speed and lookahead distance are all parameterized.)  If the robot has finished following the given trajectory (there are no remaining points in the trajectory to use as navigation targets), the robot is stopped.  

#### Path Planner - Tony Zhang
The path planning node develops the optimal path for the racecar to follow when using pure pursuit. To develop a functioning path, the A\* algorithm require a map which is converted to an occupancy grid representing whether the obstacles exist or not. The node uses a service proxy to get the map metadata and represent it as a numpy array of dimensions width by height, in pixels, with each pixel’s grayscale values stored in it’s index of the array. Furthermore, it uses scipy morphology’s binary erosion function to convert all white cells, which are unoccupied in the map, to True. The function also adds a border by buffering out the walls along the map so that the robot will not consider state spaces directly near the wall as a viable option. The path planning node also subscribes to the map topic to get the map resolution, which is used in the subscription to the Odometry topic, published by particle filter, to determine the inferred pose in map coordinates. Once the two path planning algorithms complete, the paths -- represented as a list of tuples -- are published to a trajectory file, which can be used as the input to load trajectory.

## Experimental Evaluation - TODO

Nulla tempus tempor sollicitudin. Sed id tortor vestibulum, tincidunt lorem a, suscipit lacus. Mauris vitae pretium libero, at dapibus massa. Curabitur eleifend bibendum pharetra. Nullam gravida viverra lacus eu blandit. Praesent nec odio ut magna scelerisque vulputate. Sed in libero porta, imperdiet magna maximus, efficitur urna.

### Testing Procedure - Shannon Hwang
Testing in simulation utilized the provided simulation launch file; the robot's performance was evaluated qualitatively through rviz and quantitatively by measuring the average distance between the robot's simulated pose to its correct position on its planned trajectory. Testing with the racecar took place in the Stata basement using real-time sensor input and a safety controller; the robot's performance was evaluated qualitatively through rviz by comparing its position to its visualized planned trajectory. 

#### Simulation

#### Racecar – Shannon Hwang
Testing with the racecar only took place after both trajectory tracking and path planning code had been tested in simulation, so no parameter tuning was necessary during testing.  The pure pursuit controller was tested by following a prescribed loop around the basement. To test other the pure pursuit controller's ability to follow other trajectories as well as the path planner, goal poses were set in rviz and sent to the path planner. The path planner's calculated path to the goal was then displayed in rviz, and the robot used pure pursuit to follow the planned path. The robot's performance was evaluated qualitatively by comparing its trajectories to those seen in rviz.  

### Results

#### Simulation - Akhilan Boopathy
The planner and pure pursuit controller planned and followed trajectories to a goal as verified quantitiatively and qualitatively in simulation. Given a goal pose, the planner planned an obstacle free path to the goal as illustrated in figure 4. Since the path planner took into account the motion constraints of the car, the trajectory was by design sufficiently smooth for the robot to follow. Once the trajectory was found, the robot followed the trajectory qualitatively correctly as seen in figure 4.

##### Pure Pursuit Simulation
<center><img src="assets/images/Lab6SimAll.gif" width="300" ></center>
<center>*Figure 4: A video of the robot planning and following a trajectory in simulation. The goal point is clicked in rviz. The green polygon represents the found trajectory, with an additional line directly between the start and the goal.*</center>

In addition, the robot's simulated position was quantitatively close to the true trajectory. The error of the pure pursuit controller was evaluated on a loop trajectory in the basement of Stata. of the Depending on the initial pose of the robot, the time to converge to the trajectory varied, with greater time needed for convergence for larger initial distances to the trajectory as seen in figure 5. Once converged, the robot followed the trajectory at an average distance to trajectory of 0.02 m, about one order of magitude less than the size of the robot.

##### Pure Pursuit Simulation Error
<center><img src="assets/images/Lab6PurePursuitErrorSim.png" width="400" ></center>
<center>*Figure 5: The distance to a trajectory as a simulated robot followed the trajectory using pure pursuit. The simulated robot starts at varying distances to the trajectory. The average steady state error approaches 0.02 m regardless of initial position. The trajectory used here is a loop around the basement of Stata.*</center>

#### Racecar - Akhilan Boopathy

The real robot planned and followed paths to a specified goal point as qualitatively expected. First, the performance of the real robot on a predetermined trajectory was evaluated to verify the real life performance of trajectory following. A predefined trajectory around the basement of Stata was used as a target for trajectory following. As shown in the figures below, the robot accurately followed the loop trajectory.

##### Video of Robot Following Loop Trajectory
<center><img src="assets/images/Lab6RealRobotLoop.gif" width="300" ></center>
<center>*Figure 6: A video showing a robot traveling in a loop in the Stata basement. The trajectory it is following is predefined so as to avoid walls.*</center>

##### Visualization of Robot Following Loop Trajectory
<center><img src="assets/images/Lab6LoopRviz.gif" width="300" ></center>
<center>*Figure 7: A video showing a robot in rviz traveling along a predefined trajectory. The red arrows represent the pose estimates of the robot using a particle filter to localize the robot on the map. The blue dot represents the lookahead point on the trajectory that the robot follows. The green line represents the trajectory. Note that in the visualization, the trajectory is slightly offset from the true position.*</center>

The performance of the path planner on the real robot was evaluated next. Once a goal point was manually set, the robot planned a trajectory from the robot's current position to the goal pose using the path planner. The robot then followed the planned trajectory using pure pursuit. In the figures below, the goal pose was set on the other side of an obstacle, so the robot planned and followed a trajectory around the obstacle to reach the goal pose.

##### Video of Robot Following Planned Trajectory
<center><img src="assets/images/Lab6ColumnReal.gif" width="300" ></center>
<center>*Figure 8: A video showing a robot moving around a column in the Stata basement. The trajectory it is following is planned so as to avoid the obstacle.*</center>

##### Visualization of Robot Following Planned Trajectory
<center><img src="assets/images/Lab6ColumnRviz.gif" width="300" ></center>
<center>*Figure 9: A video showing a robot in rviz planning and traveling along a planned trajectory. The start pose is the robot's current position and the goal pose is manually selected. The red arrows represent the pose estimates of the robot using a particle filter to localize the robot on the map. The blue dot represents the lookahead point on the trajectory that the robot follows. The green line represents the trajectory. Note that in the visualization, the trajectory is slightly offset from the true position.*</center>

## Lessons Learned - Akhilan Boopathy

Completing this lab required both the technical skills to design and implement a trajectory follower and path planner, as well as the communication skills necessary to effectively coordinate with team members.

### Technical Conclusions - Shannon Hwang

This lab provided insight into the theory and practice behind a pure pursuit trajectory tracker and a modified A* path planner. Implementing the algorithms solidified an understanding of the theory – for example, the calculation of a lookahead point in pure pursuit – and spurred a number of real-world realizations. As another example, though the team was initially hesistant to use A* due to its computational intensity and reliance on discretization fineness, it worked well for planning paths for the racecar. It also became clear that "rough", initially calculated trajectories needed to be refined to account for the racecar's motions constraints. 

### CI Conclusions - Shiloh Curtis, Tony Zhang

1. The team found in this lab that it was especially important to keep team members aware of each other's progress in order to coordinate work and avoid duplicated effort.  However, the to-do system used in this lab, consisting of a separate Slack channel and a Google Doc which people could update with their progress, was too complicated in that it did not have a single location to update or check on the progress of a task.  (Thus the Google Doc often did not reflect reality.)  In future labs, the team will continue to experiment with to-do tracking systems.

2. Setting up skeleton code can reduce the time for integration when the team is collaborating.
3. Student 3
