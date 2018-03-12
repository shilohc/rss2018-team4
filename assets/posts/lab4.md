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
TODO: Computer Vision – tony?

#### Cone Localization - Shannon Hwang
Given the ability to return the bounding box for an object from an image containing that object, we needed access images returned by the robot's ZED camera and relate them to real-world coordinates for the robot to navigate accordingly.

Since the camera publishes ROS Image messages, to access and extract meaningful data from the camera we had to convert its publisehd Images to a numpy array using OpenCV bridge. The images in array form were then properly translated from 2D image coordinates to their "real-world" locations in 3D space with respect to the midpoint of the robot's front wheels. We did so through by measuring 12 points in real and pixel space to compute the robot's homography matrix in the equation

$$s = \begin{bmatrix} x \\ y \\ 1 \end{bmatrix} = \begin{bmatrix}  h\_11 & h\_12 & h\_13 \\ h\_21 & h\_22 & h\_23 \\ h\_31 & h\_32 & h\_33 \\ \end{bmatrix} = \begin{bmatrix} u \\ v \\ 1 \end{bmatrix}$$

We then combined the bounding box returned by the color space image segmentation algorithm and and the coordinate transformations mentioned above in order to properly localize a cone in a given image in 3D space with respect to the midpoint of the car's front wheels. 

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

#### Cone Detection - Shiloh Curtis
The cone detection code was not itself in the form of ROS nodes, but was imported and used by the cone tracker node, as described below.  

#### Cone Localization – Shannon Hwang
The homography matrix was computed using the OpenCV function findHomography. The node subscribes to `/ZED/rgb/image_rect_color`, which publishes ROS images from the onboard ZED camera input. The ROS images were then transformed to numpy arrays using the imgmsg_to_cv2 function in the OpenCV bridge package; the numpy arrays were then fed into cd_color_segmentation() from cone_detection.py, which returned a bounding box around an object of the color of interest. That bounding box was transformed into a list of 3D points using the measured and pre-determined camera matrices, from which a distance and angle of the object of interest with respect to the car published as a ConeLocation message to `/cone_topic`.

#### Parking - Akhilan Boopathy
The parking controller subscribes to `/cone_topic`, a topic that has ConeLocation messages that specify the cone's location. After computing the desired steering angle and velocity of the robot, the parking controller publishes to `/vesc/ackermann_cmd_mux/input/navigation`. The topic `/cone_topic` may output cone locations with respect to a different reference frame than expected, such as the camera's reference frame. The calculations for the steering angle are done assuming that the cone locations are with respect to the center of the robot's back axle, so cone locations are adjusted to correct for any offset. When computing the steering angle and arc distance using the formula from the technical approach section, there are some computational issues that arise when the angle to the cone is too small or the robot is very close to the desired distance from the cone. For example, when the angle to the cone is too small, the steering radius becomes very large or is undefined, leading to potentially inaccurate or undefined results for arc distance. In the case where the robot is sufficiently close to the desired distance, the robot is commanded to stop. In the case where the angle to the cone is small, corresponding to when the cone is directly ahead, the steering angle is set to zero and the remaining arc distance is set to the difference between the actual distance and desired distance to the robot. This corresponds to an approximation of the previous formulas in the case of small angles.


#### Line Following - Eleanor Pence
The line-following code builds upon the existing ConeTracker node. In the callback for the camera topic, after the image has been converted into a numpy-array-like form, it simply checks the parameter `cone_tracker/cone_or_line`. If the value is `line`, then the numpy array should be cropped to contain just the part of the camera image from 2/10 of the image height, to 4/10 of the image height. As discussed above, this crop is enough to produce the line-following effect we are going for. 


## Experimental Evaluation - Akhilan Boopathy, Shiloh Curtis

Since not all components of this lab could easily be tested in simulation, experimental evaluation was more difficult to coordinate.  

### Testing Procedure - Akhilan Boopathy, Shiloh Curtis

#### Cone Detecting
TODO: tony?

#### Locating the Cone - Shiloh Curtis
The cone tracker was not tested in simulation, as the main point of difficulty was in real-world calibration of the homography matrix.  

The cone tracker was tested by placing the cone at various distances and angles from the robot and checking that the output coordinates and distance/angle data were correct.  Since the parking controller was already tested and working in simulation and in real life with fake cone data (artificial distance/angle values), the cone tracker was then tested by checking whether it produced the desired behavior when combined with the parking controller.  

#### Parking - Akhilan Boopathy
The parking controller was first tested in simulation before testing on the real robot. The simulation test cases were similar to the test cases on the physical robot desribed here. With the real robot, the cone was placed at a number of distances and angles from the camera. When the cone was placed away from the robot at an angle, the desired behavior was to have the robot turn in the direction of the cone. To test whether the robot could handle when the angle to the cone was small, the cone was placed directly in front of the robot, in which case the desired behavior was to have the robot point its wheels directly forward. Test cases where the cone was closer than intended to the robot was also tested, in which case the desired behavior was to have the robot move away from the robot while turning so as to point towards the cone. In addition, test cases where the cone moved were also tried. The desired behavior in this case was to have the robot continue following the cone as it was moved.

#### Line Following
TODO: eleanor?

### Results - Akhilan Boopathy

#### Cone Detecting
TODO: tony?

#### Locating the Cone – Shannon Hwang
The cone could be successfully located in real life. The parking controller consistently pointed the car towards the cone (or given bright orange object) and moved towards it. However, if the cone was situated in a dark area, the detection algorithm often had trouble determining the distance between car and cone. We compensated for the lighting we expected in Stata and normal operation by illuminating the cone.

#### Parking - Akhilan Boopathy
Once the cone was properly located, we ran the parking controller on the real robot, setting the desired distance to 0.5 meters. With the set of parameters used in simulation, the robot moved too slowly, especially when backing away from the cone when it was too close. In particular, when the speed was small but still significantly away from zero, the robot would not move. The proportion factor for the proportional controller used to control the robot's speed was increased to make the robot move as desired, while not producing oscillations in the robot's movement. Other parameters were also tweaked to match the geometry of the real robot, such as the offset between the center of the rear axle and the reference frame of cone messages.

## Lessons Learned - Akhilan Boopathy, Shannon Hwang, Shiloh Curtis
The main new technical challenges in this lab were implementing the computer vision algorithms required for finding a cone or line and calibrating the camera to correctly convert bounding boxes into points in the real world.  

The CI lessons in this lab stemmed from the fact that this lab had significantly more moving parts than the previous lab, and really tested our team's time management capabilities; we learned how to improve the processes of discussing our approach to the lab, assigning tasks, and checking in about and merging the results of various team members' work.

### Technical Conclusions - Shiloh Curtis
This lab built on the last lab's understanding of control systems and added new challenges in the form of computer vision and camera coordinate conversions.  Of these, our main sticking point was in finding the correct homography matrix for the camera.  We learned that this does not work well with too few point correspondences (12 point correspondences worked much better than 4).  

### CI Conclusions - Akhilan Boopathy, Shannon Hwang, Shiloh Curtis

1. We learned that creating a ROS framework to place our individual code contributions into was useful to ensure that the components of the lab all fit together. In particular, ensuring that different components of the lab published and subscribed messages with the same format was useful when putting components of the lab together. In general, planning out how each of our contributions would fit together before starting work was useful.
2. We also learned that having each member fully understand the entire scope of the lab was essential, even though each member would not touch every component of the lab. This became evident when we tried to merge different pieces of code for different nodes together, and there were several misunderstandings about what each piece of code built off of. 
3. Learning from the last lab, we split up the lab into various components, and assigned various components to team members. However, we neglected to set deadlines for check-ins or finishing parts of labs, which became problematic when nodes reached testing readiness before other nodes that they relied on. 

