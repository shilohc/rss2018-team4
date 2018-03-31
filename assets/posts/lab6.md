Lab 6
=====

## Overview and Motivations - Shiloh Curtis

In this lab, we implemented Monte Carlo localization (MCL).  Localization is an important problem in robotics, and Monte Carlo localization is a very popular method due to its ease of implementation and good performance.  For this lab we returned to using distance data from the LIDAR, together with a preexisting map of the Stata basement environment.  

## Proposed Approach - Shannon Hwang
The team met and discussed the lab collectively, split up to code different lab components, and came back together to debug and optimize code parameters in simulation and on the robot. Initially, each major coding component of the lab was  assigned to a different team member. We set an intermediate deadline in which all the code was to written and merged by, and spent the time after that debugging and optimizing parameters in simulation and on the robot. 

### Initial Setup - Shiloh Curtis

We assigned each major unfinished component of the localization code (the MCL algorithm's sensor model, motion model, and update step, plus helper functions that published the resulting transforms and some helpful visualizations) to a team member for initial implementation.  We then planned to combine these components and reassign team members as necessary for debugging.  Initially we tested and refined our code in simulation; over spring break, the team members that were on campus tested on the actual robot.  

### Technical Approach - Akhilan Boopathy

#### Motion Model - Akhilan Boopathy
The motion model updates the current particles representing robot positions using an action derived from the robot's odometry. The action is found by computing the difference between adjacent odometry messages then transforming it so that it is relative to the robot's current position. Then Gaussian noise is added to each component of the action since the odometry is not necessarily accurate. The variance of the Gaussian noise added to each component is different since the odometry error for each of the components is not necessarily the same. Finally the action is added to each particle by using the orientation of the particle. A transformation matrix is necessary to transform actions into the coordinate frame of the particles since actions are relative to the current robot pose.

##### Motion Model Update Equation
<center><img src="assets/images/MotionModelEqn.png" width="300" ></center>
<center>*Equation 1: The motion model has Gaussian noise added to all components.*</center>

The variables $dx$, $dy$ and $d\theta$ represent the action. The variables $N_x, N_y, N_\theta$ represent Gaussian noise for each component. $x, y, \theta$ represents an old particle and $x', y', \theta'$ represents an updated particle.

##### Motion Model Illustration
<center><img src="assets/images/MotionModelFig.png" width="300" ></center>
<center>*Figure 1: Diagram showing motion model operating on an initial pose and producing several candidate output poses with noise added. The action is also displayed without noise.*</center>

#### Sensor Model

##### Slice of Sensor Model
<center><img src="assets/images/SensorModelSlice.png" width="300" ></center>
<center>*Figure 2: Slice of sensor model with four different components: a Gaussian component around the true distance, a linear component for short measurements, a spike at the maximum possible measurement for missed measurements, and a uniform compoment for random measurements.*</center>

##### 3d plot of Complete Sensor Model
<center><img src="assets/images/SensorModel3d.png" width="300" ></center>
<center>*Figure 3: 3d plot of sensor model after normalization. The Gaussian component results in a ridge where the true distance equals the measured distance.*</center>


### ROS Implementation - Shannon Hwang

The team built around the skeleton code given for the lab, inserting appropriate code for the motion model, sensor model, MCL update, and various helper and visualization functions as needed. To reduce the runtime of the overall algorithm, operations were done in batch over all particles rather than over individual particles.

#### Motion Model - Akhilan Boopathy
The motion model operated on the particles as a numpy array rather than looping over the particles. It computed the sines and cosines of the orientations of the current particles as numpy arrays. Then, as per equation 1, it calculated the new positions using actions with Gaussian noise added. The numpy operations in the function were done in place so as to reduce the number of memory allocations per run of the function.

#### Sensor Model

#### MCL structure

#### Helper and visualization functions

## Experimental Evaluation - Shiloh Curtis

As with previous labs, we initially tested and debugged our MCL code in simulation until it worked reliably before moving to the actual robot.  Testing with the real robot took place in the Stata basement environment for which a map was provided.  

### Testing Procedure - Shannon Hwang
Testing in simulation utilized the provided autograder and headless simulator from previous labs; the robot's performance was evaluated qualitatively through rviz and quantitatively through plotting average localization error and noise.

Testing on the real robot? 

#### Simulation - Shannon Hwang

During development, performance was qualitatively evaluated by visualizing the robot in the autograder and previous lab's simulator, and quantitatively evaluated via values and graphs of autograder's evaluzation of algorithm's predicted trajectory accuracy (versus the known ground truth trajectory of a previously recorded racecar run), average localization error, sensor model probabilities, and message publishing rate. 

We ensured that the algorithm models generated expected qualitative behavior before tuning parameters to fine-tune their quantitatve behavior. While writing and deugging the motion and sensor models, for example, it was very informative to disable one of the models in the code being run and see whether the active model still provided expected behavior (roughly following a robot's trajectory with increasing amounts of noise, or "collapsing" noise based on sensor updates, respectively). 

Quantitatively, performance was ultimately tested via the accuracy of predicted versus ground truth trajectories as outputted by the autograder. The sensor model was tuned by plotting and comparing its probabilities to "ideal" values (Figure 3), and comparing average localization errors for different parameter values. 

<center><img src="assets/images/SensorModelTuning.png" width="300" ></center>
<center>*Figure 4: An example of the localization errors generated while tuning the standard deviation of the sensor model Gaussian. The parameter is chosen so as to minimize error over a period of 10 seconds from the start of a simulation.*</center>

In tuning overall parameters such as particle number, message publishing rates were considered to ensure the algorithm was able to perform in real-time and keep up with the 40 Hz input rate. 

<center><img src="assets/images/MotionSensorModelTime.png" width="300" ></center>
<center>*Figure 5: Plot of the motion and sensor model publishing rates as a function of number of particles considered.*</center>

#### Real Robot - [Insert Author]

Duis vel nunc sit amet risus consectetur dictum. Nulla mollis varius erat, vitae gravida est elementum a. Curabitur velit sapien, placerat ac scelerisque quis, ultricies at sem. Maecenas ut elit congue, condimentum lacus eu, scelerisque nunc. Curabitur mattis velit vitae sem placerat varius vel euismod leo. Cras quis elit quam. Proin scelerisque lobortis erat, eu euismod ex mattis ac. Curabitur non felis mauris. Integer mauris nisi, rutrum id finibus vel, ornare quis diam. Cras lectus nisi, pharetra ut elit at, porta auctor ex. Cras lobortis nisl leo, varius aliquet arcu sollicitudin vel. Aliquam quis nulla sapien. Donec porttitor, tortor vel iaculis vehicula, ipsum eros dictum eros, sit amet tempor orci felis vitae magna. Sed velit lacus, tincidunt sit amet quam sed, aliquam porttitor ex.

### Results - [Insert Author]

Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nam sed consequat ligula. Aliquam erat volutpat. Cras iaculis diam vitae nunc ultricies, et egestas lorem eleifend. Ut sit amet leo vitae libero maximus molestie non ac nunc. Ut ac mi ante. Vivamus convallis convallis neque, sit amet sollicitudin arcu bibendum sit amet. Phasellus finibus dolor vitae leo cursus, eu lobortis nisl blandit. Quisque tincidunt et nisi a hendrerit. Sed et nunc quis neque egestas sollicitudin. Curabitur auctor bibendum odio. Proin aliquam cursus metus, at fermentum tellus luctus vel. Morbi ut mi id augue lacinia faucibus.

#### Simulation - [Insert Author]

Duis vel nunc sit amet risus consectetur dictum. Nulla mollis varius erat, vitae gravida est elementum a. Curabitur velit sapien, placerat ac scelerisque quis, ultricies at sem. Maecenas ut elit congue, condimentum lacus eu, scelerisque nunc. Curabitur mattis velit vitae sem placerat varius vel euismod leo. Cras quis elit quam. Proin scelerisque lobortis erat, eu euismod ex mattis ac. Curabitur non felis mauris. Integer mauris nisi, rutrum id finibus vel, ornare quis diam. Cras lectus nisi, pharetra ut elit at, porta auctor ex. Cras lobortis nisl leo, varius aliquet arcu sollicitudin vel. Aliquam quis nulla sapien. Donec porttitor, tortor vel iaculis vehicula, ipsum eros dictum eros, sit amet tempor orci felis vitae magna. Sed velit lacus, tincidunt sit amet quam sed, aliquam porttitor ex.

#### Real Robot - [Insert Author]

Nulla tempus tempor sollicitudin. Sed id tortor vestibulum, tincidunt lorem a, suscipit lacus. Mauris vitae pretium libero, at dapibus massa. Curabitur eleifend bibendum pharetra. Nullam gravida viverra lacus eu blandit. Praesent nec odio ut magna scelerisque vulputate. Sed in libero porta, imperdiet magna maximus, efficitur urna.

## Lessons Learned - [Insert Author]

Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nam sed consequat ligula. Aliquam erat volutpat. Cras iaculis diam vitae nunc ultricies, et egestas lorem eleifend. Ut sit amet leo vitae libero maximus molestie non ac nunc. Ut ac mi ante. Vivamus convallis convallis neque, sit amet sollicitudin arcu bibendum sit amet. Phasellus finibus dolor vitae leo cursus, eu lobortis nisl blandit. Quisque tincidunt et nisi a hendrerit. Sed et nunc quis neque egestas sollicitudin. Curabitur auctor bibendum odio. Proin aliquam cursus metus, at fermentum tellus luctus vel. Morbi ut mi id augue lacinia faucibus.

> Duis vel nunc sit amet risus consectetur dictum. Nulla mollis varius erat, vitae gravida est elementum a. Curabitur velit sapien, placerat ac scelerisque quis, ultricies at sem. Maecenas ut elit congue, condimentum lacus eu, scelerisque nunc. Curabitur mattis velit vitae sem placerat varius vel euismod leo. Nulla tempus tempor sollicitudin. Sed id tortor vestibulum, tincidunt lorem a, suscipit lacus. Mauris vitae pretium libero, at dapibus massa. Curabitur eleifend bibendum pharetra. Nullam gravida viverra lacus eu blandit. Praesent nec odio ut magna scelerisque vulputate. Sed in libero porta, imperdiet magna maximus, efficitur urna.

### Technical Conclusions - [Insert Author]

Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nam sed consequat ligula. Aliquam erat volutpat. Cras iaculis diam vitae nunc ultricies, et egestas lorem eleifend. Ut sit amet leo vitae libero maximus molestie non ac nunc. Ut ac mi ante. Vivamus convallis convallis neque, sit amet sollicitudin arcu bibendum sit amet. Phasellus finibus dolor vitae leo cursus, eu lobortis nisl blandit. Quisque tincidunt et nisi a hendrerit. Sed et nunc quis neque egestas sollicitudin. Curabitur auctor bibendum odio. Proin aliquam cursus metus, at fermentum tellus luctus vel. Morbi ut mi id augue lacinia faucibus.

Duis vel nunc sit amet risus consectetur dictum. Nulla mollis varius erat, vitae gravida est elementum a. Curabitur velit sapien, placerat ac scelerisque quis, ultricies at sem. Maecenas ut elit congue, condimentum lacus eu, scelerisque nunc. Curabitur mattis velit vitae sem placerat varius vel euismod leo. Cras quis elit quam. Proin scelerisque lobortis erat, eu euismod ex mattis ac. Curabitur non felis mauris. Integer mauris nisi, rutrum id finibus vel, ornare quis diam. Cras lectus nisi, pharetra ut elit at, porta auctor ex. Cras lobortis nisl leo, varius aliquet arcu sollicitudin vel. Aliquam quis nulla sapien. Donec porttitor, tortor vel iaculis vehicula, ipsum eros dictum eros, sit amet tempor orci felis vitae magna. Sed velit lacus, tincidunt sit amet quam sed, aliquam porttitor ex.

### CI Conclusions - Shiloh Curtis, 

1. We learned that it's important for team members to be proactive about finding new tasks to do if they finish the work they've been assigned.  We had planned to reassign tasks once everyone had finished their component of the localization code, but some team members were never explicitly assigned new tasks and thus used their time inefficiently during lab.  

2. Student 2
3. Student 3
