Lab 6
=====

## Overview and Motivations - Shiloh Curtis

In this lab, we implemented Monte Carlo localization (MCL); localization is an important problem in robotics, and Monte Carlo localization is a very popular method due to its ease of implementation and good performance.  For this lab we returned to using distance data from the LIDAR, together with a preexisting map of the Stata basement environment.  

## Proposed Approach - Shannon Hwang
The team met and discussed the lab collectively, split up to code different lab components, and came back together to debug and optimize code parameters in simulation and on the robot. Initially, each major coding component of the lab was  assigned to a different team member. We set an intermediate deadline in which all the code was to written and merged by, and spent the time after that debugging and optimizing parameters in simulation and on the robot. 

### Initial Setup - Shiloh Curtis

We assigned each major unfinished component of the localization code (the MCL algorithm's sensor model, motion model, and update step, plus helper functions that published the resulting transforms and some helpful visualizations) to a team member for initial implementation.  We then planned to combine these components and reassign team members as necessary for debugging.  Initially we tested and refined our code in simulation; over spring break, the team members that were on campus tested on the actual robot.  

### Technical Approach - Akhilan Boopathy

#### Motion Model - Akhilan Boopathy
The motion model updates the current particles representing robot positions using an action derived from the robot's odometry. The action is found by computing the difference between adjacent odometry messages then transforming it so that it is relative to the robot's current position. Then Gaussian noise is added to each component of the action since the odometry is not necessarily accurate. The variance of the Gaussian noise added to each component is different since the odometry error for each of the components is not necessarily the same. Finally the action is added to each particle by using the orientation of the particle. A transformation matrix is necessary to transform actions into the coordinate frame of the particles since actions are relative to the current robot pose.

##### Motion Model Update Equation
<center><img src="assets/images/MotionModelEqn.JPG" width="300" ></center>
<center>*Equation 1: The motion model has Gaussian noise added to all components.*</center>

The variables dx, dy and dtheta represent the action. The variables Nx, Ny, Ntheta represent Gaussian noise for each component. x, y, theta represent an old particle and x', y', theta' represent an updated particle. Note that in the motion model, each input particle results in a single output particle. In the figure below, multiple particles are shown for the single input to illustrate that the motion model adds random noise. Adding noise results in the output particles clustered around the pose if no noise was added. Due to different noise parameters per particle component, the variance of the output particles varies depending on the direction considered.

##### Motion Model Illustration
<center><img src="assets/images/MotionModelFig.png" width="300" ></center>
<center>*Figure 1: Diagram showing motion model operating on an initial pose and producing several candidate output poses with noise added. The action is also displayed without noise.*</center>

#### Sensor Model - Chenxing(Tony) Zhang
The sensor model had four components corresponding to different types of error.

The first error to consider is that something not accounted for in the given map, such as a person or chair, may be in between the robot and the nearest occupied space of the map. This error is modeled by the downward sloping linear part of the distribution between 0px and the given ground truth distance.

The second error is that our LiDAR sensor may not be 100% accurate at measuring the distance from itself to another object. We used a gaussian distribution centered around the given ground truth distance to account for that inaccuracy as is shown in Figure 2.

The third error is that the LiDAR may give random readings or have noise. Essentially this means that, given a ground truth distance, every distance has some probability of being the measured distance. No measured distance in FIgure 2 has a probability of zero.

The fourth error is that the LiDAR sensor has a max range. If the ground truth distance is larger than the max range, the LiDAR will most likely return the max range value. In Figure 2, this is modeled by the large spike at the end of the distribution.

Once we figured out how a cross section of the probability distribution should be modeled, creating the entire lookup table was just a matter of computing the cross section at each given ground truth distance and then normalizing the entire distribution. The final result is shown in Figure 2.

##### Slice of Sensor Model
<center><img src="assets/images/SensorModelSlice.png" width="300" ></center>
<center>*Figure 2: Slice of sensor model with four different components: a Gaussian component around the true distance, a linear component for short measurements, a spike at the maximum possible measurement for missed measurements, and a uniform compoment for random measurements.*</center>

##### 3d plot of Complete Sensor Model
<center><img src="assets/images/SensorModel3d.png" width="300" ></center>
<center>*Figure 3: 3d plot of sensor model after normalization. The Gaussian component results in a ridge where the true distance equals the measured distance.*</center>


### ROS Implementation - Shannon Hwang

The team built around the skeleton code given for the lab, inserting appropriate code for the motion model, sensor model, MCL update, and various helper and visualization functions as needed. To reduce the runtime of the overall algorithm, operations were done in batch over all particles rather than over individual particles.

#### Motion Model - Akhilan Boopathy
The motion model operated on the particles as a numpy array rather than looping over the particles. It computed the sines and cosines of the orientations of the current particles as numpy arrays. Then, as per equation 1, it calculated the new positions using actions with Gaussian noise added. The numpy operations in the function were done in place so as to reduce the number of memory allocations per run of the function. The action necessary for the motion model computation was found by differencing consecutive odometry messages from `/vesc/odom` and transforming them relative to the robot's current odometry so that the action is with respect to the robot's current pose.

#### Sensor Model Precomputation - Chenxing(Tony) Zhang
We used the precomputed sensor model to compute the probability of an observation ogiven a location and a heading angle - which we call a pose denoted by x as follows:

$$ p(0|x\_i^t) = \Pi \_j \, p(o\_j|x\_i^t) \, \forall \, j \in \\{subsampled \; angles\\} $$

Where the probability \\( p(o\_j|x\_i^t) \\) is essentially the value given by the precomputed probability distribution as described in the previous section and is computed for each particle in the map. The resulting probability \\(p(o|x\_i^t)\\) was then assigned as the weight of particle i at timestep t denoted as \\( w\_i^t \\). 

After applying the sensor model update to the particles, we sampled from all of the new particles based on their weights meaning particles with higher weights were more likely to be chosen. We then updated our estimate based on these high probability particles and computed an expected pose estimate by using our sampled particles along with their weights.

This portion of the code performed a lot of computation - seen by the fact that it accounted for close to 60% of the total time for the entire update step. In order to speed up computations, we exported all of the core components of this function to a C library - rangeLibC which was provided to us by Corey Walsh. This removed the need of python for loops which made our updates fast since all arrays were contiguous blocks of memory and allowed us to take advantage of caching since the arrays exhibited strong locality. This was a key reason we were able to obtain a high update rate without needing to use our car’s GPU.

We first tested our sensor model by printing out the weight of a single particle at different poses to make sure that it had the desired results. This led to us discovering that we had an error in our precomputed sensor model that resulted in probabilities of zero. Once we were satisfied that the one particle was behaving correctly, we added more particles. We found that we were not properly resampling since the particles were not converging to the car. Once we fixed these problems, we tuned the standard deviation of the gaussian distribution in the precomputed model to achieve better results. 

#### Overall structure – Shannon Hwang
The code was structured to reduce the amount of memory allocations. When the particle filter initializes, it precomputes the sensor model once and initializes subscribers to LIDAR (`/scan`) and odometry data (`/vesc/odom`), publishers for various visualization topics, and a publisher to publish the inferred pose (`/pf/pose/odom`). 
The first lidar and odometry callbacks trigger one-time initialization of arrays for downsampled scans and odometry that are updated on subsequent callbacks. Since the odometry topic publishes less frequently than the lidar, an MCL update is performed at the end of the callback. In the update, poses are randomly chosen from the existing particles (according to established weights) updated according to the motion model, then weighted according to the sensor model. 

#### Helper and visualization functions - Eleanor Pence, Shiloh Curtis

Once the MCL update is complete, the inferred pose is computed, so it can be used in visualization and to build transformations from the map frame to the robot's frame. Inferred pose is computed as a weighted average of the particles. The transform is built directly from that, though the angle included in that inferred pose must be converted to quanternion form by use of a built-in function, `Utils.angle_to_quaternion`, from the provided lab code. 

Next we publish visualizations of the robot's position and the particle cloud. Visualizing the robot's position is simple, as the PoseStamped ROS message type contains all the same information as the inferred position. We then call np.random_choice on the array of particles to downsample the particle cloud and publish the position of the particles remaining - the goal is to publish sufficient particles so we can understand what the localization algorithm is doing, but not so many particles that RViz becomes overwhelmed trying to display them all. Finally, we publish a LaserScan message containing fake scan data created by casting rays from the robot's inferred position to obstacles on the map, which simulates what the scan data would look like if the inferred position were correct.  

## Experimental Evaluation - Shiloh Curtis

As with previous labs, we initially tested and debugged our MCL code in simulation until it worked reliably before moving to the actual robot.  Testing with the real robot took place in the Stata basement environment for which a map was provided.  

### Testing Procedure - Shannon Hwang, Shiloh Curtis
Testing in simulation utilized the provided autograder and headless simulator from previous labs; the robot's performance was evaluated qualitatively through rviz and quantitatively through plotting average localization error and noise.

While the autograder was also run on the real robot to create a submission for Gradescope, most testing with the racecar took place in the Stata basement using sensor input collected in real time.  The scan processor node written in Lab 3 was used to adjust for the Velodyne angle offset.  The robot's performance was evaluated qualitatively using rviz.  

#### Simulation - Shannon Hwang

During development, performance was qualitatively evaluated by visualizing the robot in the autograder and previous lab's simulator, and quantitatively evaluated via values and graphs of autograder's evaluzation of algorithm's predicted trajectory accuracy (versus the known ground truth trajectory of a previously recorded racecar run), average localization error, sensor model probabilities, and message publishing rate. 

We ensured that the algorithm models generated expected qualitative behavior before tuning parameters to fine-tune their quantitatve behavior. While writing and deugging the motion and sensor models, for example, it was very informative to disable one of the models in the code being run and see whether the active model still provided expected behavior (roughly following a robot's trajectory with increasing amounts of noise, or "collapsing" noise based on sensor updates, respectively). 

Quantitatively, performance was ultimately tested via the accuracy of predicted versus ground truth trajectories as outputted by the autograder. The sensor model was tuned by plotting and comparing its probabilities to "ideal" values (Figure 3), and comparing average localization errors for different parameter values. 

<center><img src="assets/images/SensorModelTuning.png" width="300" ></center>
<center>*Figure 4: An example of the localization errors generated while tuning the standard deviation of the sensor model Gaussian. The parameter is chosen so as to minimize error over a period of 10 seconds from the start of a simulation.*</center>

In tuning overall parameters such as particle number, message publishing rates were considered to ensure the algorithm was able to perform in real-time and keep up with the 40 Hz input rate. 

<center><img src="assets/images/MotionSensorModelTime.png" width="300" ></center>
<center>*Figure 5: Plot of the motion and sensor model publishing rates as a function of number of particles considered.*</center>

#### Real Robot - Shiloh Curtis

Only once localization was working well in simulation did we test using the real racecar, so no additional parameter tuning was necessary to achieve reasonable results.  Performance was tested qualitatively, using real-time sensor input, by placing the robot in the Stata basement, starting rviz, and observing whether the resulting localization data approximated the correct position.  

### Results - Akhilan Boopathy, Shiloh Curtis

#### Simulation - Akhilan Boopathy
The algorithm was verified both qualitatively and quantitiatively, and performed as expected with the inferred pose of the robot approximately tracking the true position. The intitial poses of the particles is clicked near the true position with some variance added. The particles then track the true pose of the robot using the motion model and sensor model. When the car turns, the particles spread out horizontally intially due to greater uncertainty in position, but the particles converge again as the sensor model increases the weights of the particles closer to the true position.

<center><img src="assets/images/LocalizationSim.gif" width="300" ></center>
<center>*Figure 6: A video of the MCL algorithm running in simulation. Red arrows represent the particles and the green arrow represents the inferred pose, which is the average pose of the particles. White dots show a fake scan showing where the scan would be if the robot were actually at the inferred position. The rainbow colored true scan coinciding with the actual walls of the map indicates that the algorithm is correctly localizing the robot.*</center>

In order to quantitavively evaluate the performance of the algorithm in simulation, the odometry of the simulation was used as a ground truth since in simulation, the odometry is exactly accurate. The localization error was computed by taking the magnitude of the error between the true and inferred position. Since errors in orientation eventually result in errors in position, the orientation error was not evaluated seperately. A circular trajectory was chosen to standardize the evalutation metric. Since the trajectory is periodic and uniformly symmetric over the entire trajectory, a constant average steady state error was expected. This steady state error is used as the evaluation metric.

<center><img src="assets/images/LocalizationSimCircle.gif" width="300" ></center>
<center>*Figure 7: A video showing a circular trajectory used for quantiative evaluation of the MCL algorithm in simulation. Red arrows represent the particles and the green arrow represents the inferred pose, which is the average pose of the particles. The true simulated car pose is represented by the car. The red line represents the actual scan, and the white dots represent a fake scan showing the points where a scan would project if the robot was at the inferred position.*</center>

The steady state error of the robot driving in circular paths was about 0.3 meters. This error was on the same order of magnitude as the size of the car and one order of magnitude less than the corridors that the real robot was driven in. Because the initial positions of the car in the tests differed, the convergence time changed from run to run.

<center><img src="assets/images/LocalizationError.png" width="300" ></center>
<center>*Figure 8: Plot of localization error over time when the robot is driving in circular paths. Different steering angles and speeds of trajectory were evaluated. The initial positions of the robot were different in each trajectory resulting in different times for convergence.*</center>

#### Real Robot - Shiloh Curtis

On the racecar, localization was verified qualitatively.  Given an initial pose it performed well; the error in the returned position remained less than 0.4 meters while the car was driven down a hallway, most of which was steady-state error.  (Unlike the simulator, the real world does not provide ground-truth odometry data, so it was difficult to quantitatively measure the localization error with more accuracy.)

<center><img src="assets/images/LocalizationErrorReal.png" width="300" ></center>
<center>*Figure 9: Screenshot of rviz showing localization error between map and laserscan data.  This error is relatively small in comparison to the size of features in the hallway, and is an order of magnitude less than the hallway width.*</center>

The robot did not perform as well when not given an accurate initialization pose or when kidnapped.  When not given an initialization pose, or when moved to a completely different part of the basement, it was unable to converge on the correct location.  However, if it was moved a short distance (on the order of 1 meter), it was usually able to correctly relocalize.  

For good performance with SLAM, further parameter tuning to reduce error would be necessary.  However, for navigation using a pre-made map, our localization algorithm in its current state would work as long as the robot is not picked up and moved.  

<center><img src="assets/images/split_screen_localization.gif" width="600" ></center>
<center>*Figure 10: Localization running on the racecar in the Stata basement.  On the left is a video of the robot in the hallway; on the right, the corresponding rviz visualization.*</center>

## Lessons Learned - Eleanor Pence

Implementing the Monte Carlo Localization algorithm requires a firm grasp of both the theory and practical considerations of the algorithm, but dealing with such complexity also required excellent teamwork skills.  

### Technical Conclusions - Shannon Hwang

In this lab, we learned about the theory behind Monte Carlo localization and put it into practice. During implementation, we learned about a number of real-world considerations of the algorithm, such as how important it was to minimize the number of memory allocations, use numpy operations, and generally prioritize efficient code operation when processing large amounts of real-world data. In addition, we did learn that in some cases it was much easier to debug less efficient, but more clearly written code, and consider optimization later. We also utilized a lot more of ROS' graphing and visualization tools than in the past lab, which were extremely useful in debugging the sensor model and tuning the many parameters in the algorithm. 

### CI Conclusions - Shiloh Curtis, Shannon Hwang, Eleanor Pence

1. We learned that it's important for team members to be proactive about finding new tasks to do if they finish the work they've been assigned.  We had planned to reassign tasks once everyone had finished their component of the localization code, but some team members were never explicitly assigned new tasks and thus used their time inefficiently during lab.  

2. We also learned that setting concrete intermediate deadlines was an important and extremely useful step to take, but that team members should also ideally update the team on progress (or more importantly, any roadblocks) as internal deadlines approached. 

3. In the same vein, even though debugging work is often difficult to parallelize, it was important to find ways to split up that work so that all team members' time could be fully utilized. 
