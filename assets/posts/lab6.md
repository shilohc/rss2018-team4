Lab 6
=====

## Overview and Motivations - Shiloh Curtis

In this lab, we implemented Monte Carlo localization.  Localization is an important problem in robotics, and Monte Carlo localization is a very popular method due to its ease of implementation and good performance.  For this lab we returned to using distance data from the LIDAR, together with a preexisting map of the Stata basement environment.  

## Proposed Approach - Shiloh Curtis

We approached this lab by assigning each major unfinished component of the localization code (the MCL algorithm's sensor model, motion model, and update step, plus helper functions that published the resulting transforms and some helpful visualizations) to a team member for initial implementation.  We then planned to combine these components and reassign team members as necessary for debugging.  Initially we tested and refined our code in simulation; over spring break, the team members that were on campus tested on the actual robot.  

### Initial Setup - [Insert Author]

Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nam sed consequat ligula. Aliquam erat volutpat. Cras iaculis diam vitae nunc ultricies, et egestas lorem eleifend. Ut sit amet leo vitae libero maximus molestie non ac nunc. Ut ac mi ante. Vivamus convallis convallis neque, sit amet sollicitudin arcu bibendum sit amet. Phasellus finibus dolor vitae leo cursus, eu lobortis nisl blandit. Quisque tincidunt et nisi a hendrerit. Sed et nunc quis neque egestas sollicitudin. Curabitur auctor bibendum odio. Proin aliquam cursus metus, at fermentum tellus luctus vel. Morbi ut mi id augue lacinia faucibus.

<center>[Pictures]</center>

Duis vel nunc sit amet risus consectetur dictum. Nulla mollis varius erat, vitae gravida est elementum a. Curabitur velit sapien, placerat ac scelerisque quis, ultricies at sem. Maecenas ut elit congue, condimentum lacus eu, scelerisque nunc. Curabitur mattis velit vitae sem placerat varius vel euismod leo. Cras quis elit quam. Proin scelerisque lobortis erat, eu euismod ex mattis ac. Curabitur non felis mauris. Integer mauris nisi, rutrum id finibus vel, ornare quis diam. Cras lectus nisi, pharetra ut elit at, porta auctor ex. Cras lobortis nisl leo, varius aliquet arcu sollicitudin vel. Aliquam quis nulla sapien. Donec porttitor, tortor vel iaculis vehicula, ipsum eros dictum eros, sit amet tempor orci felis vitae magna. Sed velit lacus, tincidunt sit amet quam sed, aliquam porttitor ex.

Nulla tempus tempor sollicitudin. Sed id tortor vestibulum, tincidunt lorem a, suscipit lacus. Mauris vitae pretium libero, at dapibus massa. Curabitur eleifend bibendum pharetra. Nullam gravida viverra lacus eu blandit. Praesent nec odio ut magna scelerisque vulputate. Sed in libero porta, imperdiet magna maximus, efficitur urna.

### Technical Approach - Akhilan Boopathy

#### Sensor Model


##### Slice of Sensor Model
<center><img src="assets/images/SensorModelSlice.png" width="300" ></center>
<center>*Figure 1: Slice of sensor model with four different components: a Gaussian component around the true distance, a linear component for short measurements, a spike at the maximum possible measurement for missed measurements, and a uniform compoment for random measurements.*</center>

##### 3d plot of Complete Sensor Model
<center><img src="assets/images/SensorModel3d.png" width="300" ></center>
<center>*Figure 2: 3d plot of sensor model after normalization. The Gaussian component results in a ridge where the true distance equals the measured distance.*</center>


#### Motion Model - Akhilan Boopathy

##### Motion Model Illustration
<center><img src="assets/images/MotionModelFig.png" width="300" ></center>
<center>*Figure 3: Diagram showing motion model operating on an initial pose and producing several candidate output poses with noise added. The action is also displayed without noise.*</center>


### ROS Implementation - [Insert Author]

Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nam sed consequat ligula. Aliquam erat volutpat. Cras iaculis diam vitae nunc ultricies, et egestas lorem eleifend. Ut sit amet leo vitae libero maximus molestie non ac nunc. Ut ac mi ante. Vivamus convallis convallis neque, sit amet sollicitudin arcu bibendum sit amet. Phasellus finibus dolor vitae leo cursus, eu lobortis nisl blandit. Quisque tincidunt et nisi a hendrerit. Sed et nunc quis neque egestas sollicitudin. Curabitur auctor bibendum odio. Proin aliquam cursus metus, at fermentum tellus luctus vel. Morbi ut mi id augue lacinia faucibus.

Duis vel nunc sit amet risus consectetur dictum. Nulla mollis varius erat, vitae gravida est elementum a. Curabitur velit sapien, placerat ac scelerisque quis, ultricies at sem. Maecenas ut elit congue, condimentum lacus eu, scelerisque nunc. Curabitur mattis velit vitae sem placerat varius vel euismod leo. Cras quis elit quam. Proin scelerisque lobortis erat, eu euismod ex mattis ac. Curabitur non felis mauris. Integer mauris nisi, rutrum id finibus vel, ornare quis diam. Cras lectus nisi, pharetra ut elit at, porta auctor ex. Cras lobortis nisl leo, varius aliquet arcu sollicitudin vel. Aliquam quis nulla sapien. Donec porttitor, tortor vel iaculis vehicula, ipsum eros dictum eros, sit amet tempor orci felis vitae magna. Sed velit lacus, tincidunt sit amet quam sed, aliquam porttitor ex.

<center>[Pictures]</center>

Nulla tempus tempor sollicitudin. Sed id tortor vestibulum, tincidunt lorem a, suscipit lacus. Mauris vitae pretium libero, at dapibus massa. Curabitur eleifend bibendum pharetra. Nullam gravida viverra lacus eu blandit. Praesent nec odio ut magna scelerisque vulputate. Sed in libero porta, imperdiet magna maximus, efficitur urna.

## Experimental Evaluation - Shiloh Curtis

As with previous labs, we initially tested and debugged our MCL code in simulation until it worked reliably before moving to the actual robot.  Testing with the real robot took place in the Stata basement environment for which a map was provided.  

### Testing Procedure - [Insert Author]

#### Simulation - [Insert Author]

Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nam sed consequat ligula. Aliquam erat volutpat. Cras iaculis diam vitae nunc ultricies, et egestas lorem eleifend. Ut sit amet leo vitae libero maximus molestie non ac nunc. Ut ac mi ante. Vivamus convallis convallis neque, sit amet sollicitudin arcu bibendum sit amet. Phasellus finibus dolor vitae leo cursus, eu lobortis nisl blandit. Quisque tincidunt et nisi a hendrerit. Sed et nunc quis neque egestas sollicitudin. Curabitur auctor bibendum odio. Proin aliquam cursus metus, at fermentum tellus luctus vel. Morbi ut mi id augue lacinia faucibus.

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
