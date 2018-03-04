Lab 3
=====

## Overview and Motivations - Shannon Hwang

Lab 3 marked the start of working with our actual, physical robot. As such, it entailed familiarizing ourselves with the necessary processes for interacting with it, such as powering the robot on and off, connecting its sensors, networking into it to save programs, and visualizing what it "saw" on our own laptops. As a culmination of all this, we had to make sure the robot could properly follow the "wall follower" code written in the previous lab. Finally, we wrote a safety controller to prevent high-speed crashes into obstacles and protect the car's expensive hardware against future crashes. 

## Proposed Approach - Shannon Hwang

### Initial Setup - Shannon Hwang

As none of the team members had interacted with the car before and we all have to interact with the robot in the future, all of the team familiarized ourselves with the robot. Then, we split up to work on the wall follower and safety controller.

All team members were involved in initially setting up the robot and making sure we could see the robot's "world" through rviz on our individual laptops. We discussed and decided to use the wall follower code with the highest score in Lab 2 on the robot. Then, half of the team worked on importing Lab 2's wall follower code into the robot, and half discussed the safety controller, which a team member wrote up. 

### Technical Approach - Shannon Hwang, Akhilan Boopathy
The safety controller is designed to stop the robot at a reasonable speed when it violates a minimum "safe" distance from an obstacle, and to prevent any further forward motion at that distance. 

#### Wall follower:
The wall follower uses laser scan data from the car to estimate the position and angle of the wall. The wall follower considers only the points from the laser scan on the side of the car the wall is expected to be on. Linear regression is used on these points to estimate the angle and distance from the wall. Once the wall angle and distance are estimated, the car changes its angle so as to move more parallel with the wall as well as to achieve the desired distance from the wall. This corresponds to a PD controller. The car's speed is set to be equal to a desired constant speed.

#### Safety controller:
Since the safety controller is supposed to prevent crashes, it only considers the "forward" section of the laser scan data (a 120-degree swath centered on the front of the robot). To prevent abrupt stops, the safety controller acts in two distance "ranges". If the robot is within .35 m of an obstacle (slightly more than the robot's width), it stops completely, and the safety controller prevents any future command telling the robot to move forward. To ensure that the robot stops at a reasonable speed, if the robot is within .7 m of an obstacle (2 times the stopping distance) its speed is proportional to its remaining distance from the stopping threshhold.

<center>![Simulated robot approaching wall and stopping](assets/images/safety_controller_sim.gif)</center>

### ROS Implementation - Shannon Hwang

#### Wall follower:
The wall follower subscribes to the `/scan` topic and publishes to `/vesc/ackermann_cmd_mux/input/navigation`. Depending on the side the wall follower is commanded to follow, the laser scan data is sliced into the range pi/3 to 2pi/3 or -pi/3 to -2pi/3. Points from these ranges are converted to Cartesian coordinates, and numpy is used to perform a linear regression on the data points. The output from the regression is converted to estimates of the angle of the wall and distance from the wall. These estimates are then used in a PD controller to control the angle of the car.

#### Safety controller: 
The safety controller subscribes to the `/scan` and `/vesc/high_level/ackermann_cmd_mux/output` topics, and publishes to `/vesc/low_level/ackermann_cmd_mux/input/safety`. It slices the laser scan data into the range -pi/3 and pi/3, then compares the minimum distance from that range to the threshhold safety distance. If the navigation program is trying to drive the car forward (as determined from incoming messages on `/vesc/high_level/ackermann_cmd_mux/output`) and the car is within .7 m or .35 m of an obstacle, the car publishes a command of either a reduced or 0 velocity, respectively. 

<center>[TODO: Pictures]</center>

## Experimental Evaluation - Shiloh Curtis

The safety controller was tested first in simulation and then on the robot.  Additionally, all testing was done at a low speed.  These precautions aimed to avoid collision damage to the robot during testing.  

### Testing Procedure - Shiloh Curtis

Since teleop commands would override the safety controller, a test script was written that continuously published commands to drive forward on `/vesc/ackermann_cmd_mux/input/navigation`.  This script was used to drive the robot towards a wall.  If the robot stopped before reaching the wall, the safety controller was deemed successful.  Once the safety controller passed this test when the simulated robot was driven head-on towards a wall, it was moved to the real robot.  

TODO: finish this section after testing on real robot

### Results - Akhilan Boopathy

#### Wall follower: 
Using the wall follower code directly from lab 2 resulted in the car moving either into the wall or away from the wall instead of moving parallel to the wall as intended. The laser scan data was returned in multiple messages with each message containing only a portion of the laser scan data. As a result, each laser scan message contained missing values that the original code did not properly account for. This issue was resolved by merging together multiple messages. The laser scan data also was offset by an angle corresponding to the orientation of the lidar on the car. The wall follower was adjusted to correct for the offset.

TODO: finish this section after testing on real robot

Duis vel nunc sit amet risus consectetur dictum. Nulla mollis varius erat, vitae gravida est elementum a. Curabitur velit sapien, placerat ac scelerisque quis, ultricies at sem. Maecenas ut elit congue, condimentum lacus eu, scelerisque nunc. Curabitur mattis velit vitae sem placerat varius vel euismod leo. Cras quis elit quam. Proin scelerisque lobortis erat, eu euismod ex mattis ac. Curabitur non felis mauris. Integer mauris nisi, rutrum id finibus vel, ornare quis diam. Cras lectus nisi, pharetra ut elit at, porta auctor ex. Cras lobortis nisl leo, varius aliquet arcu sollicitudin vel. Aliquam quis nulla sapien. Donec porttitor, tortor vel iaculis vehicula, ipsum eros dictum eros, sit amet tempor orci felis vitae magna. Sed velit lacus, tincidunt sit amet quam sed, aliquam porttitor ex.

Nulla tempus tempor sollicitudin. Sed id tortor vestibulum, tincidunt lorem a, suscipit lacus. Mauris vitae pretium libero, at dapibus massa. Curabitur eleifend bibendum pharetra. Nullam gravida viverra lacus eu blandit. Praesent nec odio ut magna scelerisque vulputate. Sed in libero porta, imperdiet magna maximus, efficitur urna.

## Lessons Learned - [Insert Author]

TODO

Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nam sed consequat ligula. Aliquam erat volutpat. Cras iaculis diam vitae nunc ultricies, et egestas lorem eleifend. Ut sit amet leo vitae libero maximus molestie non ac nunc. Ut ac mi ante. Vivamus convallis convallis neque, sit amet sollicitudin arcu bibendum sit amet. Phasellus finibus dolor vitae leo cursus, eu lobortis nisl blandit. Quisque tincidunt et nisi a hendrerit. Sed et nunc quis neque egestas sollicitudin. Curabitur auctor bibendum odio. Proin aliquam cursus metus, at fermentum tellus luctus vel. Morbi ut mi id augue lacinia faucibus.

> Duis vel nunc sit amet risus consectetur dictum. Nulla mollis varius erat, vitae gravida est elementum a. Curabitur velit sapien, placerat ac scelerisque quis, ultricies at sem. Maecenas ut elit congue, condimentum lacus eu, scelerisque nunc. Curabitur mattis velit vitae sem placerat varius vel euismod leo. Nulla tempus tempor sollicitudin. Sed id tortor vestibulum, tincidunt lorem a, suscipit lacus. Mauris vitae pretium libero, at dapibus massa. Curabitur eleifend bibendum pharetra. Nullam gravida viverra lacus eu blandit. Praesent nec odio ut magna scelerisque vulputate. Sed in libero porta, imperdiet magna maximus, efficitur urna.

### Technical Conclusions - [Insert Author]

TODO

Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nam sed consequat ligula. Aliquam erat volutpat. Cras iaculis diam vitae nunc ultricies, et egestas lorem eleifend. Ut sit amet leo vitae libero maximus molestie non ac nunc. Ut ac mi ante. Vivamus convallis convallis neque, sit amet sollicitudin arcu bibendum sit amet. Phasellus finibus dolor vitae leo cursus, eu lobortis nisl blandit. Quisque tincidunt et nisi a hendrerit. Sed et nunc quis neque egestas sollicitudin. Curabitur auctor bibendum odio. Proin aliquam cursus metus, at fermentum tellus luctus vel. Morbi ut mi id augue lacinia faucibus.

Duis vel nunc sit amet risus consectetur dictum. Nulla mollis varius erat, vitae gravida est elementum a. Curabitur velit sapien, placerat ac scelerisque quis, ultricies at sem. Maecenas ut elit congue, condimentum lacus eu, scelerisque nunc. Curabitur mattis velit vitae sem placerat varius vel euismod leo. Cras quis elit quam. Proin scelerisque lobortis erat, eu euismod ex mattis ac. Curabitur non felis mauris. Integer mauris nisi, rutrum id finibus vel, ornare quis diam. Cras lectus nisi, pharetra ut elit at, porta auctor ex. Cras lobortis nisl leo, varius aliquet arcu sollicitudin vel. Aliquam quis nulla sapien. Donec porttitor, tortor vel iaculis vehicula, ipsum eros dictum eros, sit amet tempor orci felis vitae magna. Sed velit lacus, tincidunt sit amet quam sed, aliquam porttitor ex.

### CI Conclusions - [Insert Author]

TODO

1. Student 1
2. Student 2
3. Student 3
