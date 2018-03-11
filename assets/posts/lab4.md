Lab 4
=====

## Overview and Motivations - Akhilan Boopathy

Before lab 4, we had primarily worked with the LIDAR sensor to detect the robot's environment. With this lab 4, we tried to use the robot's onboard forward pointing camera to detect objects in the environment. The camera allows for detection of objects when the LIDAR is either unusable for some reason or unable to detect the relevant objects. For instance, the LIDAR is unable to detect differences in color, while proper processing of data from a camera would allow for this. We had to implement methods to locate objects in the environment such as color space image segmentation. We also had to implement a way to locate an object found in the camera in the reference frame of the physical robot. As tests of our ability to detect and find the location of objects, we had to make sure the robot could park in front of an orange cone at a desired distance using just the camera. In addition, we had to make sure the robot could follow a curved red line on the floor.

## Proposed Approach - Akhilan Boopathy

### Initial Setup - Akhilan Boopathy

For this lab, we were not provided with a ROS framework, so our team had to create our own. One team member worked on creating the ROS framework. The other team members split up to work on each of the other components of the lab: cone detection, locating and visualizing the cone, parking the robot and following the line.

### Technical Approach - Akhilan Boopathy

#### Cone Detecting

#### Locating the Cone

#### Parking - Akhilan Boopathy
The goal for the parking controller was to have the robot's final state be at specified distance from the cone while being oriented towards the cone. This specifies a circle of possible final locations for the robot. A constant steering radius is chosen such that the robot ends up on one of these locations. Given a constant cone location, the robot moves in a circular arc to the goal location.

##### Parking Geometry
<center><img src="assets/images/ParkingDiagram.JPG" width="300" ></center>
<center>*Figure 1: Diagram of the robot's calculated circulat arc trajectory to reach the desired distance from the cone.*</center>

Under a bicycle model for the robot with wheel distance L, given the distance and angle to the cone and the desired distance to the cone, the steering angle is given by:

$$steering\,angle = tan^{-1}(\frac{2\,L\,distance\,\sin(angle)}{distance^2-desired^2})$$

Given the steering radius R, the distance to the goal point along the circular arc is given by:

$$arc\,distance = |R|cos^{-1}(\frac{2R^2+desired^2-distance^2}{2R\sqrt{desired^2+R^2}})-Rtan^{-1}(\frac{desired}{R})$$

The speed of the robot is controlled proportionally to the remaining arc distance. This approach also works when the robot is too close to the cone and needs to move backwards.

##### Parking Simulation
<center><img src="assets/images/ParkerSim.gif" width="300" ></center>
<center>*Figure 2: Simulated robot parking in front of cone*</center>

#### Line Following



### ROS Implementation - Akhilan Boopathy
The code was split into two ROS packages: one for tracking the cone, and one for driving the robot. Each package contained relevant ROS nodes which communicated using various ROS topics.

#### Cone Detecting

#### Locating the Cone

#### Parking - Akhilan Boopathy
The parking controller subscribes to `/cone_topic`, a topic that has ConeLocation messages that specify the cone's location. After computing the desired steering angle and velocity of the robot, the parking controller publishes to `/vesc/ackermann_cmd_mux/input/navigation`. The topic `/cone_topic` may output cone locations with respect to a different reference frame than expected, such as from the camera's reference frame. The calculations for the steering angle are done assuming that the cone locations are with respect to the center of the robot's back axle, so cone locations are adjusted to correct for any offset. When computing the steering angle and arc distance using the formula from the technical approach section, there are some computational issues that arise when the angle to the cone is too small or the robot is very close to the desired distance from the cone. In the case where the robot is sufficiently close to the desired distance, the robot is commanded to stop. In the case where the angle to the cone is small, corresponding to when the cone is directly ahead, the steering angle is set to zero and the remaining arc distance is set to the difference between the actual distance and desired distance to the robot. This corresponds to an approximation of the previous formulas in the case of small angles.


#### Line Following


## Experimental Evaluation - Akhilan Boopathy

Nulla tempus tempor sollicitudin. Sed id tortor vestibulum, tincidunt lorem a, suscipit lacus. Mauris vitae pretium libero, at dapibus massa. Curabitur eleifend bibendum pharetra. Nullam gravida viverra lacus eu blandit. Praesent nec odio ut magna scelerisque vulputate. Sed in libero porta, imperdiet magna maximus, efficitur urna.

### Testing Procedure - Akhilan Boopathy

#### Cone Detecting

#### Locating the Cone

#### Parking - Akhilan Boopathy
The parking controller was first tested in simulation before testing on the real robot. The simulation test cases were similar to the test cases on the physical robot desribed here. With the real robot, the cone was placed at a number of distances and angles from the camera. When the cone was placed away from the robot at an angle, the desired behavior was to have the robot turn in the direction of the cone. To test whether the robot could handle when the angle to the cone was small, the cone was placed directly in front of the robot, in which case the desired behavior was to have the robot point its wheels directly forward. Test cases where the cone was closer than intended to the robot was also tested, in which case the desired behavior was to have the robot move away from the robot while turning so as to point towards the cone. In addition, test cases where the cone moved were also tried. The desired behavior in this case was to have the robot continue following the cone as it was moved.

#### Line Following

### Results - [Insert Author]

Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nam sed consequat ligula. Aliquam erat volutpat. Cras iaculis diam vitae nunc ultricies, et egestas lorem eleifend. Ut sit amet leo vitae libero maximus molestie non ac nunc. Ut ac mi ante. Vivamus convallis convallis neque, sit amet sollicitudin arcu bibendum sit amet. Phasellus finibus dolor vitae leo cursus, eu lobortis nisl blandit. Quisque tincidunt et nisi a hendrerit. Sed et nunc quis neque egestas sollicitudin. Curabitur auctor bibendum odio. Proin aliquam cursus metus, at fermentum tellus luctus vel. Morbi ut mi id augue lacinia faucibus.


Duis vel nunc sit amet risus consectetur dictum. Nulla mollis varius erat, vitae gravida est elementum a. Curabitur velit sapien, placerat ac scelerisque quis, ultricies at sem. Maecenas ut elit congue, condimentum lacus eu, scelerisque nunc. Curabitur mattis velit vitae sem placerat varius vel euismod leo. Cras quis elit quam. Proin scelerisque lobortis erat, eu euismod ex mattis ac. Curabitur non felis mauris. Integer mauris nisi, rutrum id finibus vel, ornare quis diam. Cras lectus nisi, pharetra ut elit at, porta auctor ex. Cras lobortis nisl leo, varius aliquet arcu sollicitudin vel. Aliquam quis nulla sapien. Donec porttitor, tortor vel iaculis vehicula, ipsum eros dictum eros, sit amet tempor orci felis vitae magna. Sed velit lacus, tincidunt sit amet quam sed, aliquam porttitor ex.

Nulla tempus tempor sollicitudin. Sed id tortor vestibulum, tincidunt lorem a, suscipit lacus. Mauris vitae pretium libero, at dapibus massa. Curabitur eleifend bibendum pharetra. Nullam gravida viverra lacus eu blandit. Praesent nec odio ut magna scelerisque vulputate. Sed in libero porta, imperdiet magna maximus, efficitur urna.

## Lessons Learned - [Insert Author]

Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nam sed consequat ligula. Aliquam erat volutpat. Cras iaculis diam vitae nunc ultricies, et egestas lorem eleifend. Ut sit amet leo vitae libero maximus molestie non ac nunc. Ut ac mi ante. Vivamus convallis convallis neque, sit amet sollicitudin arcu bibendum sit amet. Phasellus finibus dolor vitae leo cursus, eu lobortis nisl blandit. Quisque tincidunt et nisi a hendrerit. Sed et nunc quis neque egestas sollicitudin. Curabitur auctor bibendum odio. Proin aliquam cursus metus, at fermentum tellus luctus vel. Morbi ut mi id augue lacinia faucibus.

> Duis vel nunc sit amet risus consectetur dictum. Nulla mollis varius erat, vitae gravida est elementum a. Curabitur velit sapien, placerat ac scelerisque quis, ultricies at sem. Maecenas ut elit congue, condimentum lacus eu, scelerisque nunc. Curabitur mattis velit vitae sem placerat varius vel euismod leo. Nulla tempus tempor sollicitudin. Sed id tortor vestibulum, tincidunt lorem a, suscipit lacus. Mauris vitae pretium libero, at dapibus massa. Curabitur eleifend bibendum pharetra. Nullam gravida viverra lacus eu blandit. Praesent nec odio ut magna scelerisque vulputate. Sed in libero porta, imperdiet magna maximus, efficitur urna.

### Technical Conclusions - [Insert Author]

Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nam sed consequat ligula. Aliquam erat volutpat. Cras iaculis diam vitae nunc ultricies, et egestas lorem eleifend. Ut sit amet leo vitae libero maximus molestie non ac nunc. Ut ac mi ante. Vivamus convallis convallis neque, sit amet sollicitudin arcu bibendum sit amet. Phasellus finibus dolor vitae leo cursus, eu lobortis nisl blandit. Quisque tincidunt et nisi a hendrerit. Sed et nunc quis neque egestas sollicitudin. Curabitur auctor bibendum odio. Proin aliquam cursus metus, at fermentum tellus luctus vel. Morbi ut mi id augue lacinia faucibus.

Duis vel nunc sit amet risus consectetur dictum. Nulla mollis varius erat, vitae gravida est elementum a. Curabitur velit sapien, placerat ac scelerisque quis, ultricies at sem. Maecenas ut elit congue, condimentum lacus eu, scelerisque nunc. Curabitur mattis velit vitae sem placerat varius vel euismod leo. Cras quis elit quam. Proin scelerisque lobortis erat, eu euismod ex mattis ac. Curabitur non felis mauris. Integer mauris nisi, rutrum id finibus vel, ornare quis diam. Cras lectus nisi, pharetra ut elit at, porta auctor ex. Cras lobortis nisl leo, varius aliquet arcu sollicitudin vel. Aliquam quis nulla sapien. Donec porttitor, tortor vel iaculis vehicula, ipsum eros dictum eros, sit amet tempor orci felis vitae magna. Sed velit lacus, tincidunt sit amet quam sed, aliquam porttitor ex.

### CI Conclusions - Akhilan Boopathy

1. We learned that creating a ROS framework to place our individual code contributions into was useful to ensure that the components of the lab all fit together. In particular, ensuring that different components of the lab published and subscribed messages with the same format was useful when putting components of the lab together. In general, planning out how each of our contributions would fit together before starting work was useful.
2. Student 2
3. Student 3

## (Optional) Future Work - [Insert Author]

Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nam sed consequat ligula. Aliquam erat volutpat. Cras iaculis diam vitae nunc ultricies, et egestas lorem eleifend. Ut sit amet leo vitae libero maximus molestie non ac nunc. Ut ac mi ante. Vivamus convallis convallis neque, sit amet sollicitudin arcu bibendum sit amet. Phasellus finibus dolor vitae leo cursus, eu lobortis nisl blandit. Quisque tincidunt et nisi a hendrerit. Sed et nunc quis neque egestas sollicitudin. Curabitur auctor bibendum odio. Proin aliquam cursus metus, at fermentum tellus luctus vel. Morbi ut mi id augue lacinia faucibus.

Nulla tempus tempor sollicitudin. Sed id tortor vestibulum, tincidunt lorem a, suscipit lacus. Mauris vitae pretium libero, at dapibus massa. Curabitur eleifend bibendum pharetra. Nullam gravida viverra lacus eu blandit. Praesent nec odio ut magna scelerisque vulputate. Sed in libero porta, imperdiet magna maximus, efficitur urna.
