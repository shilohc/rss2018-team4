Lab 3
=====

## Overview and Motivations - Shannon Hwang

Lab 3 marked the start of working with our actual, physical robot. As such, it entailed familiarizing ourselves with the necessary processes for interacting with it, such as powering the robot on and off, connecting its sensors, networking into it to save programs, and visualizing what it "saw" on our own laptops. As a culmination of all this, we had to make sure the robot could properly follow the "wall follower" code written in the previous lab. Finally, we wrote a safety controller to prevent high-speed crashes into obstacles and protect the car's expensive hardware against future crashes. 

## Proposed Approach - Shannon Hwang, Akhilan Boopathy, Shiloh Curtis

### Initial Setup - Shannon Hwang

As none of the team members had interacted with the robot before, all of the team familiarized ourselves with the robot by setting up the robot's various connections and making sure we could see the robot's "world" through rviz on our individual laptops. We discussed and decided to use the wall follower code with the highest score in Lab 2 on the robot. Then, half of the team worked on importing Lab 2's wall follower code into the robot, and half discussed the safety controller, which a team member wrote up. 

### Technical Approach - Akhilan Boopathy, Shannon Hwang, Shiloh Curtis
The wall follower, as specified in lab 2, commands to robot to move along (parallel to) a wall on the robot's left or right side at a given distance away from the wall. The safety controller stops the robot at a reasonable speed when it violates a minimum "safe" distance from an obstacle, and to prevent any further forward motion at that distance. 

#### Wall follower:
The wall follower uses laser scan data from the car to estimate the position and angle of the wall. The wall follower considers only the points from the laser scan on the side of the car the wall is expected to be on, since these are the relevant points for estimating the wall's position. Since the wall is generally expected to be straight, linear regression is used on these points to estimate the angle and distance from the wall. Once the wall angle and distance are estimated, the car changes its angle so as to move more parallel with the wall as well as to achieve the desired distance from the wall. This corresponds to a PD controller, an approach that worked previously in simulation in lab 2. The car's speed is set to be equal to a desired constant speed.

#### Safety controller:
Since the safety controller is supposed to prevent crashes, it only considers the "forward" section of the laser scan data (a 120-degree swath centered on the front of the robot). To prevent abrupt stops, the safety controller acts in two distance "ranges". If the robot is within .35 m of an obstacle (slightly more than the robot's width), it stops completely, and the safety controller prevents any future command telling the robot to move forward. To ensure that the robot stops at a reasonable speed, if the robot is within .7 m of an obstacle (2 times the stopping distance) its speed is proportional to its remaining distance from the stopping threshhold.

##### Safety Controller Simulation
<center>![Simulated robot approaching wall and stopping](assets/images/safety_controller_sim.gif)</center>
<center>*Figure 1: Simulated robot approaching wall and stopping*</center>

### ROS Implementation - Akhilan Boopathy, Shiloh Curtis, Shannon Hwang
Both the wall follower and safety controller subscribe to laser scan data from the onboard LIDAR in order to determine where the robot can safely go, and publish to some sort of navigation topic in order to tell the robot where to go. However, the safety controller also listens to what the current navigation program is telling the robot to do and publishes to a higher priority navigation topic than the navigation program so that it can stop the robot in case the navigation program tells it to drive into an obstacle.

#### Wall follower:
The wall follower subscribes to the `/scan` topic to recieve laser scan data and publishes to `/vesc/ackermann_cmd_mux/input/navigation`. Depending on the side the wall follower is commanded to follow, the laser scan data is sliced into the range pi/3 to 2pi/3 or -pi/3 to -2pi/3. This ensures that points not in the relevant areas do not affect the estimate of the wall's position. Points from these ranges are converted to Cartesian coordinates, and the numpy library is used to perform a linear regression on the data points. The output from the regression is converted to estimates of the angle of the wall and distance from the wall. These estimates are then used in a PD controller to control the angle of the car.

#### Safety controller: 
The safety controller subscribes to the `/scan` and `/vesc/high_level/ackermann_cmd_mux/output` topics, and publishes to `/vesc/low_level/ackermann_cmd_mux/input/safety`. It slices the laser scan data into the range -pi/3 and pi/3 (corresponding to the "front" section of the robot), then compares the minimum distance from that range to the threshhold safety distance. If the navigation program is trying to drive the car forward (as determined from incoming messages on `/vesc/high_level/ackermann_cmd_mux/output`) and the car is within .7 m or .35 m of an obstacle, the car publishes a command of either a reduced or 0 velocity, respectively. 

## Experimental Evaluation - Shiloh Curtis

The safety controller was tested first in simulation and then on the robot.  Additionally, all testing was done at a low speed.  These precautions aimed to avoid collision damage to the robot during testing.  

### Testing Procedure - Shiloh Curtis, Akhilan Boopathy

#### Wall follower:
The wall follower was tested in a long hallway with a straight, nonreflective wall with a few shallow doorways. A number of initial positions and angles of the robot were tried, including the robot pointing towards and away from the wall at a moderately steep angle. The wall follower was successful if it eventually reached the desired distance away from the wall while driving parallel to the wall.

#### Safety controller:
Since teleop commands would override the safety controller, a test script was written that continuously published commands to drive forward on `/vesc/ackermann_cmd_mux/input/navigation`.  This script was used to drive the robot towards a wall.  If the robot stopped before reaching the wall, the safety controller was deemed successful.  Once the safety controller passed this test when the simulated robot was driven head-on towards a wall, it was moved to the real robot.  

### Results - Akhilan Boopathy

#### Wall follower: 
Using the wall follower code directly from lab 2 resulted in the car moving either into the wall or away from the wall instead of moving parallel to the wall as intended. The laser scan data was returned in multiple messages with each message containing only a portion of the laser scan data. As a result, each laser scan message contained missing values that the original code did not properly account for. The laser scan data also was offset by an angle corresponding to the orientation of the lidar on the car. In order to resolve these two issues, another node was created to process the laser scan data. This node merging together multiple messages into a single message with complete data. It also corrected for the angular offset in the laser scan data. The new messages, published to a new `/processed_scan` topic, was used in the wall follower. 

##### Processed Scan Data
<center><img src="assets/images/Hallway_scan.JPG" width="400" ></center>
<center>*Figure 2: Rviz screenshot of processed scan data while the robot is in a hallway. All laser scan points are in each message, and the orientation is correct.*</center>

The parameters for the wall follower's PD controller also were suboptimal initially since they were optimised for the simulation instead of the actual robot. After the parameters were tuned, the wall follower was able to follow the wall at the expected distance as intended.

##### Wall Follower Test
<center><img src="assets/images/wall_follower.gif" width="400" ></center>
<center>*Figure 3: Robot turning to follow wall at desired distance.*</center>

#### Safety controller:
The initial implementation of the safety controller did not work as intended since laser scan points closer than approximately 0.5 meters were not accurately measured. As a result, once objects were closer than this radius to the car, the car would not consistently be able to detect these objects. In order to correct for this issue, the safety controller was modified to stop further away from objects. After this change, the safety controller worked as intended. 

##### Safety Controller Test
<center><img src="assets/images/safety_controller.gif" width="400" ></center>
<center>*Figure 4: Robot slowing down and stopping after spotting an obstacle.*</center>

## Lessons Learned - Shannon Hwang

From a technical standpoint, an important lesson we learned was how to properly interface with the robot as a whole, and with the Velodyne LIDAR in particular. We also learned how to think about preventing the robot from crashing into obstacles, and determine estimations related to that goal (for example, estimating how far away a reasonable stopping distance for the robot was.)

From a teamwork and collaborative standpoint, we learned that it was somewhat infeasible to have multiple people working on creating a single piece of code (for example, the safety controller) or physically interacting with the robot at the same time, so team effort was spent most efficiently completing the tasks we had to do in parallel. We also learned about the challenges of coordinating five people, and realized that we need to coordinate team time much more in advance than the margins we left for this lab. 


### Technical Conclusions - Shannon Hwang

In this lab, we learned standard procedures for interacting with the robot as mentioned above. Most critically, we learned about how to properly process the data from one particular part of the robot: the Velodyne LIDAR sensor. Originally, we did not know that the Velodyne sensor published laser scan data that was offset from the standard orientation of the car, or that published laser scan data in fragmented chunks; this caused the car to behave strangely with our team members' wall followers. Thus, we learned that multiple laser scan messages had to be merged to provide complete information about the car's orientation, and that the laser scan data had to be offset in order to work with our previously visualized coordinate frames. 

When discussing and implementing the safety controller, we also learned how to think about how to account for our robot's physical presence; for example, we eventually decided that the minimum "safe" distance to a wall would probably be better calculated based on the robot's width, rather than length, as that mattered more when it turned. 
### CI Conclusions - Shannon Hwang, Akhilan Boopathy, Shiloh Curtis

1. We learned that it was more efficient to parallelize the completion of coding or testing on the robot. Though multiple people could discuss and debate code at high-level, once we actually started writing it, it became clear that it was very difficult to have multiple people simultaneously writing the same piece of code – especially shorter pieces like the safety controller. Additionally, since we only had one robot, we relied on using the simulated racecar to develop and "test" most of the safety controller while another part of the team worked on debugging the wall follower on the physical robot. 

2. We also learned that splitting up our code into multiple pieces was helpful to make more efficient progress. For example, we initially had a procedure to process laser scan data in the code for our wall follower. By shifting the scan processing code to a seperate ROS node, some team members were able to work on the scan processing while others were able to work on the wall follower. We also found that even when one team member was working on multiple components, modularizing our code helped us evaluate each component seperately.

3. We learned that it's important to discuss and consciously delegate tasks rather than silently splitting up to work on them.  Otherwise multiple people start writing the same code independent of each other, while other tasks are left undone.  If two people are working on the same thing at once, we needed to make sure they both knew that, so they could work together.  
