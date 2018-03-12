Lab 4
=====

## Overview and Motivations - Akhilan Boopathy

Before lab 4, we had primarily worked with the LIDAR sensor to detect the robot's environment. With this lab, we tried to use the robot's onboard forward pointing camera to detect objects in the environment. The camera allows for detection of objects when the LIDAR is either unusable or unable to detect relevant objects. For instance, the LIDAR is unable to detect differences in color, while a camera (given proper processing of its data) can. So, we had to implement methods such as color space image segmentation to locate objects in the environment. We also had to implement a way to locate an object found in the camera in the reference frame of the physical robot. As tests of our ability to detect and find the location of objects, we had to make sure the robot could park in front of an orange cone at a desired distance using just the camera. In addition, we had to make sure the robot could follow a curved red line on the floor.

## Proposed Approach - Akhilan Boopathy

### Initial Setup - Akhilan Boopathy

For this lab, we were not provided with a ROS framework, so our team had to create our own. One team member worked on creating the ROS framework. The other team members split up to work on each of the other components of the lab: cone detection, locating and visualizing the cone, parking the robot and following the line.

### Technical Approach - Akhilan Boopathy, Shannon Hwang
We attempted to pipeline as much of the lab as possible. Team members were intially assigned independent nodes such as creating the lab's ROS framework, completing and testing various computer vision algorithms, writing nodes for image and coordinate transformations, and writing nodes for parking and line-following, with the understanding that some tasks would take longer than others and that teammates could be reassigned. 

Parking and line-following controllers were written under the assumption that they would receive accurate coordinates for what to follow. Once the image transformations worked and computer vision algorithms worked passably, they were combined to create a cone detection and localization ndoe. That node was then used to implement the parking and line-following nodes on the real robot. 

#### Cone Detection
Computer Vision – tony?

#### Cone Localization 

##### Image and Cooordinate Transformations - Shannon Hwang
Given the ability to return the bounding box for an object from an image containing that object, we needed access images returned by the robot's ZED camera and relate them to real-world coordinates for the robot to navigate accordingly.

Since the camera publishes ROS Image messages, to access and extract meaningful data from the camera we had to convert its publisehd Images to a numpy array using OpenCV bridge. The images in array form were then properly translated from 2D image coordinates to their "real-world" locations in 3D space with respect to the midpoint of the robot's front wheels. We did so through by measuring the camera's rotation and translation (extrinsic parameters), and finding the pre-calibrated intrinsic camera matrix on the robot. We then used the matrices representing the intrinsic and extrinsic parameters in the following equation: 

$$s\begin{bmatrix}
    u \\
    v \\
    1 \\
\end{bmatrix}  = 
\begin{bmatrix}
    f_x 0 c_x \\
    0 f_y c_y \\
    0 0 1 \\
\end{bmatrix}
\begin{bmatrix}
    r_{11} r_{12} r_{13} t_1\\
    r_{21} r_{22} r_{23} t_2\\
    r_{31} r_{32} r_{33} t_3\\
\end{bmatrix}
\begin{bmatrix}
    X\\
    Y\\
    Z\\
    1\\
\end{bmatrix}
$$

We then combined the bounding box returned by the color space image segmentation algorithm and and the coordinate transformations mentioned above in order to properly localize a cone in a given image in 3D space with respect to the midpoint of the car's front wheels. As proof that we could do so, we published an RVIZ Marker representing the cone and published Images visualizing the cone detection algorithm, and published the distance and angle of the cone with respect to the car to be used in the parking and line following controllers.

#### Parking - Akhilan Boopathy
The goal for the parking controller was to have the robot's final state be at a specified distance from the cone while being oriented towards the cone. This specifies a circle of possible final locations for the robot. A constant steering radius is chosen such that the robot ends up on one of these locations. Given a constant cone location, the robot moves in a circular arc to the goal location. Note that this is different from pure pursuit of the goal: under pure pursuit the robot does not necessarily have the correct orientation when it reaches the goal. In this approach, the goal point and steering angle are chosen simultaneously so the robot always points towards the cone.

##### Parking Geometry
<center><img src="assets/images/ParkingDiagram.JPG" width="300" ></center>
<center>*Figure 1: Diagram of the robot's calculated circular arc trajectory to reach the desired distance from the cone.*</center>

Under a bicycle model for the robot with wheel distance L, given the distance and angle to the cone and the desired distance to the cone, the steering radius and steering angle are given by:

$$R = \frac{distance^2-desired^2}{2\,distance\,\sin(angle)}$$
$$steering\,angle = tan^{-1}(\frac{L}{R})$$

The distance to the goal point along the circular arc is given by:

$$arc\,distance = |R|cos^{-1}(\frac{2R^2+desired^2-distance^2}{2R\sqrt{desired^2+R^2}})-Rtan^{-1}(\frac{desired}{R})$$

The speed of the robot is controlled proportionally to the remaining arc distance. This approach also works when the robot is too close to the cone and needs to move backwards.

##### Parking Simulation
<center><img src="assets/images/ParkerSim.gif" width="300" ></center>
<center>*Figure 2: Simulated robot parking in front of cone*</center>

#### Line Following - Eleanor Pence
It turns out that it is possible to use an extremely similar algorithm for line-following as for cone-parking. The main difference is that, before any coordinate transforms occur but after the image is converted from a ROS Image message to a numpy-like array format, the input image should be cropped to only a small horizontal slice of the full image. This creates a situation where, whenever the line is visible to the camera within that slice, the robot perceives that there is always an orange patch at a certain fixed distance away. Using the parking algorithm, the robot will orient itself correctly and move towards that perceived orange patch, but since the patch is actually a line, the robot continues this process so long as there is line to follow. All that is needed, then, is a single parameter to determine if the robot should find a cone and park there, or if it should follow a line visible to it. 


### ROS Implementation - Akhilan Boopathy
The code was split into two ROS packages: one for tracking the cone, and one for driving the robot. Each package contained relevant ROS nodes which communicated using various ROS topics.

#### Cone Detection

#### Cone Localization – Shannon Hwang
The node subscribes to `ZED/rgb/image_rect_color`, which publishes ROS images from the onboard ZED camera input. The ROS images were then transformed to numpy arrays using the imgmsg_to_cv2 function in the OpenCV bridge package; the numpy arrays were then fed into cd_color_segmentation() from cone_detection.py, which returned a bounding box around an object of the color of interest. That bounding box was transformed into a list of 3D points using the measured and pre-determined camera matrices, from which a distance and angle of the object of interest with respect to the car published as a ConeLocation message to `/cone_topic`.

#### Parking - Akhilan Boopathy
The parking controller subscribes to `/cone_topic`, a topic that has ConeLocation messages that specify the cone's location. After computing the desired steering angle and velocity of the robot, the parking controller publishes to `/vesc/ackermann_cmd_mux/input/navigation`. The topic `/cone_topic` may output cone locations with respect to a different reference frame than expected, such as the camera's reference frame. The calculations for the steering angle are done assuming that the cone locations are with respect to the center of the robot's back axle, so cone locations are adjusted to correct for any offset. When computing the steering angle and arc distance using the formula from the technical approach section, there are some computational issues that arise when the angle to the cone is too small or the robot is very close to the desired distance from the cone. For example, when the angle to the cone is too small, the steering radius becomes very large or is undefined, leading to potentially inaccurate or undefined results for arc distance. In the case where the robot is sufficiently close to the desired distance, the robot is commanded to stop. In the case where the angle to the cone is small, corresponding to when the cone is directly ahead, the steering angle is set to zero and the remaining arc distance is set to the difference between the actual distance and desired distance to the robot. This corresponds to an approximation of the previous formulas in the case of small angles.


#### Line Following - Eleanor Pence

The line-following code builds upon the existing ConeTracker node. In the callback for the camera topic, after the image has been converted into a numpy-array-like form, it simply checks the parameter `cone_tracker/cone_or_line`. If the value is `line`, then the numpy array should be cropped to contain just the part of the camera image from 2/10 of the image height, to 4/10 of the image height. As discussed above, this crop is enough to produce the line-following effect we are going for. 


## Experimental Evaluation - Akhilan Boopathy

Nulla tempus tempor sollicitudin. Sed id tortor vestibulum, tincidunt lorem a, suscipit lacus. Mauris vitae pretium libero, at dapibus massa. Curabitur eleifend bibendum pharetra. Nullam gravida viverra lacus eu blandit. Praesent nec odio ut magna scelerisque vulputate. Sed in libero porta, imperdiet magna maximus, efficitur urna.

### Testing Procedure - Akhilan Boopathy

#### Cone Detecting

TODO: RVIZ Marker, Cone detection visualization images

#### Locating the Cone

#### Parking - Akhilan Boopathy
The parking controller was first tested in simulation before testing on the real robot. The simulation test cases were similar to the test cases on the physical robot desribed here. With the real robot, the cone was placed at a number of distances and angles from the camera. When the cone was placed away from the robot at an angle, the desired behavior was to have the robot turn in the direction of the cone. To test whether the robot could handle when the angle to the cone was small, the cone was placed directly in front of the robot, in which case the desired behavior was to have the robot point its wheels directly forward. Test cases where the cone was closer than intended to the robot was also tested, in which case the desired behavior was to have the robot move away from the robot while turning so as to point towards the cone. In addition, test cases where the cone moved were also tried. The desired behavior in this case was to have the robot continue following the cone as it was moved.

#### Line Following

### Results - [Insert Author]

Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nam sed consequat ligula. Aliquam erat volutpat. Cras iaculis diam vitae nunc ultricies, et egestas lorem eleifend. Ut sit amet leo vitae libero maximus molestie non ac nunc. Ut ac mi ante. Vivamus convallis convallis neque, sit amet sollicitudin arcu bibendum sit amet. Phasellus finibus dolor vitae leo cursus, eu lobortis nisl blandit. Quisque tincidunt et nisi a hendrerit. Sed et nunc quis neque egestas sollicitudin. Curabitur auctor bibendum odio. Proin aliquam cursus metus, at fermentum tellus luctus vel. Morbi ut mi id augue lacinia faucibus.


Duis vel nunc sit amet risus consectetur dictum. Nulla mollis varius erat, vitae gravida est elementum a. Curabitur velit sapien, placerat ac scelerisque quis, ultricies at sem. Maecenas ut elit congue, condimentum lacus eu, scelerisque nunc. Curabitur mattis velit vitae sem placerat varius vel euismod leo. Cras quis elit quam. Proin scelerisque lobortis erat, eu euismod ex mattis ac. Curabitur non felis mauris. Integer mauris nisi, rutrum id finibus vel, ornare quis diam. Cras lectus nisi, pharetra ut elit at, porta auctor ex. Cras lobortis nisl leo, varius aliquet arcu sollicitudin vel. Aliquam quis nulla sapien. Donec porttitor, tortor vel iaculis vehicula, ipsum eros dictum eros, sit amet tempor orci felis vitae magna. Sed velit lacus, tincidunt sit amet quam sed, aliquam porttitor ex.

Nulla tempus tempor sollicitudin. Sed id tortor vestibulum, tincidunt lorem a, suscipit lacus. Mauris vitae pretium libero, at dapibus massa. Curabitur eleifend bibendum pharetra. Nullam gravida viverra lacus eu blandit. Praesent nec odio ut magna scelerisque vulputate. Sed in libero porta, imperdiet magna maximus, efficitur urna.

## Lessons Learned - [Insert Author]

Importing python packages into ROS Framework


### Technical Conclusions - [Insert Author]


### CI Conclusions - Akhilan Boopathy, Shannon Hwang

1. We learned that creating a ROS framework to place our individual code contributions into was useful to ensure that the components of the lab all fit together. In particular, ensuring that different components of the lab published and subscribed messages with the same format was useful when putting components of the lab together. In general, planning out how each of our contributions would fit together before starting work was useful.
2. We also learned that having each member fully understand the entire scope of the lab was essential, even though each member would not touch every component of the lab. This became evident when we tried to merge different pieces of code for different nodes together, and there were several misunderstandings and code conflicts. 
3. Student 3

