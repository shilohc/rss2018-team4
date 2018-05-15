Final Project
=====

## Overview and Motivations - Shannon Hwang

Throughout the course, planning and control algorithms were implemented to autonomously drive a robotic RC car; however, they were largely implemented under the assumption that a LIDAR capable of accurately determining distances to obstacles was available. However, lidars are expensive and relatively inaccessible; it would be optimal to devise some sort of navigation policy capable of directing the robot given only images from relatively inexpensive cameras. Thus, our hypothesis for this lab was as follows:

### Hypothesis/Goal - A deep neural network can be trained such that it is capable of autonomously driving a robot through an unknown environment using only camera images of the environment. 

## Proposed Approach - Akhilan Boopathy
The overall approach to determine naviation from images taken from the robot's camera was to preprocess images, run the data through a neural network and determine how to control the car from the neural network output. The first step was to decide the specifics of the pipeline used to train and test the neural network. This involved deciding how to represent actions in the neural network, how to label actions, and how to train and validate the neural network.

### Initial Setup - [Insert Author]
#### Code Pipeline - Eleanor Pence

The first step in the process of training the DNN to drive with camera input only was to design the labeling algorithm. This work was done during an all-group brainstorming session in lab. Once a labeling method had been decided on, Shiloh and Shannon began the implementation of the labeling algorithm, while Eleanor and Akhilan worked on prototyping the DNN and writing scripts for training and inference. 

After that, the team entered a phase in which the labeling scheme and training were repeatedly refined. In each case, the labeling code was modified, and a team member would re-label the data. This resulted in a pickled version of several numpy arrays, which included the newly labeled data, being added to the git repository, which was to re-train the DNN. Real-world testing and further discussion with the team would typically yield further potential ideas for improvements, which would then be implemented in much the same way before, until desired performance was achieved.  

### Technical Approach - Akhilan Boopathy
In order to use the pipeline on the car, the neural network had to be trained using image and lidar data collected from the robot. Lidar scans were used to label whether particular steering angles at each robot pose led to collisions or not. The labeled data was then used to train the network. Finally, the output of the neural network was used to select the best steering angle on the robot.


#### Image Labeling - Shiloh Curtis

Images were labeled with actions (driving commands) and the minimum distance between any point on the trajectory resulting from that action and an obstacle (based on the collected scan data).  Further processing using distance thresholds then added booleans representing whether the robot was expected to collide with an obstacle as a result of that action.  

The action definition consists of a steering angle; the speed and distance along the trajectory are pre-defined, so this completely defines a simple trajectory.  Images were then labeled by sampling points from the trajectory and finding the minimum distance to any obstacle, then taking the overall minimum distance along the trajectory.  Robot size was accounted for in the postprocessing step, which models the robot as a circle and checks if the obstacle distance threshold is larger than the robot "radius".  

##### Robot Actions and Laser Scan

<center><img src="assets/images/SteeringAngleLaserScan.png" width="300" ></center>
<center>*Figure 1: A visualization of 25 circular arcs corresponding to possible steering angles the robot could take. Each arc represents to the robot moving 1.5 m with the steering angle. The robot's current position is where all the circular arcs intersect. The blue dots represent the laser scan. During labeling, the arcs which are sufficiently close the laser scan are labeled as being in collision.*</center>

#### Image Preprocessing - Eleanor Pence
Before they could be used in either training or real-time inference, images underwent a preprocessing step. Images were resized to 45x80 and transformed into grayscale, and then the image data was normalized.  Resizing of the images improved the speed and memory usage of the network at inference time, but during training, it also allowed the entire dataset of approximately 20,000 images to be loaded into memory at once, which made training much simpler.  

#### Neural Network Architecture - Eleanor Pence

The neural network architecture we used was relatively simple. The layers of the network were fully connected; the first three layers had 200 units each and used rectified linear units (ReLU) as an activation function, and the last layer, a "logits" layer that gave the actual classifications, had 20 units (equivalent to the number of actions the robot could take), and used a sigmoid activation function. 

#### Hyperparameter Selection - [Insert Author]
Hyperparameters were selected to maximize validation accuracy while minimizing training time.

##### Accuracy vs. Number of Layers

<center><img src="assets/images/LayersAccuracy.png" width="300" ></center>
<center>*Figure 2: The validation accuracy resulting from training a neural network to predict steering angle collision labels while varying the number of layers. The number of units per layer remained fixed at 200 units. Each neural network was trained for 50 epochs.*</center>

##### Training Time vs. Number of Layers

<center><img src="assets/images/LayersTime.png" width="300" ></center>
<center>*Figure 3: The training time for training a neural network to predict steering angle collision labels while varying the number of layers. The number of units per layer remained fixed at 200 units. Each neural network was trained for 50 epochs. The training time increases roughly linearly with the number of layers. Variation is due to random fluctuations in the training speed.*</center>

##### Accuracy vs. Number of Units

<center><img src="assets/images/UnitsAccuracy.png" width="300" ></center>
<center>*Figure 4: The validation accuracy resulting from training a neural network to predict steering angle collision labels while varying the number of layers. The number of layers remained fixed at 3. Each neural network was trained for 50 epochs.*</center>

##### Training Time vs. Number of Units

<center><img src="assets/images/UnitsTime.png" width="300" ></center>
<center>*Figure 5: The training time for training a neural network to predict steering angle collision labels while varying the number of layers. The number of layers remained fixed at 3. Each neural network was trained for 50 epochs. The training time increases roughly linearly with the number of units. Variation is due to random fluctuations in the training speed.*</center>


#### Neural Network Training - Akhilan Boopathy

The neural network was trained using a subset of the labeled images. The data was split into a training and validation set, with 95% of the data randomly selected as the training set. For each epoch of training, the training set was split into batches of size 1000, and the network was trained with each batch. Training was performed using stochastic gradient descent with a learning rate of 0.01 and momentum of 0.9. After each epoch of training, which constitutes one full pass through the training set, the loss and accuracy on the validation set were evaluated. Training was performed for sets of 1000 epochs each, until validation loss started increasing, indicating overfitting.

### ROS Implementation - Shannon Hwang

Image transforming, data labeling, network training, and robot steering were implemented as separate ROS nodes and Python scripts. The image transformation node took camera data from previously recorded rosbag files, and published transformed images. Data labeling involved calculating probabilities of collision for given steering angles given transformed images and laser scan data from the rosbag files and storing probabilities, alongside images, in pickle files. Network training utilized TensorFlow to train a specified network architecture from the pickle files. Finally, a robot steering node fed real-time camera input on the robot to a trained network model, and output steering commands to the robot.

#### Image Labeling - Shiloh Curtis

The training dataset consisted initially of a collection of bagfiles containing image and lidar data.  Data were collected from these bagfiles using the rosbag API; the lidar data was used to determine which trajectories were safe for the robot to traverse, and thereby label the images.  

##### Fraction of Collisions by Angle

<center><img src="assets/images/LabeledCollisionAverages.png" width="300" ></center>
<center>*Figure 6: The fraction of labeled images resulting in a collision for each action in the labeled dataset. Probabilities of collision are lower for angles near zero because in much of the dataset, the robot is parallel to a straight hallway. Probabilities of collision for positive angles are higher than for negative angles, indicating a bias in the dataset towards left turns.*</center>


#### Driving Using NN Output - [Insert Author]

##### Probability of Collision vs. Steering Angle

<center><img src="assets/images/TestSetProbabilities.png" width="300" ></center>
<center>*Figure 7: Probabilities of collision for a neural network trained to output probabilities of collision for each steering angle action a robot could take. Collision probabilities are taken by running random sample images from a test set through the trained neural network. Near 0 degrees, there are few points with a very high probability of collision because in the dataset, the robot is often parallel to a straight hallway. For high angles, there are few points with a low probability of collision because of a bias in the dataset towards left turns.*</center>

##### Multiplicative Bias

<center><img src="assets/images/Bias.png" width="300" ></center>
<center>*Figure 8: A bias multiplied by the outputs of a neural network that outputs probabilities of collision for each steering angle action. The biases are lower for angles closer to 0, making selected actions more likely to be drive the robot straight.*</center>

## Experimental Evaluation - Shannon Hwang

Correctness of images via visual inspection of transformed images and predicted probabilities of collision; accuracy of the nerual network was verified by computing accuracy and loss on a validation data set.

### Testing Procedure - [Insert Author]

#### Image Labeling - Shannon Hwang

<center><img src="assets/images/image_transform.gif" width="300" ></center>
<center>*Figure 9: The transformed images used as neural network input (left) versus original camera feed images (right).*</center>

Images were transformed to be 45x80 grayscale images; this was verified by subscribing to the publisher feeding grayscale, resized images to the neural network and comparing a video of said images to the full camera feed. 

#### Neural Network Verification - Akhilan Boopathy
The neural network was verified using a validation set before evaluating the neural network's performance on the robot. The neural network's performance was evaluated by computing the accuracy and loss values on a validation set. Accuracy is the fraction of the time that the action with the lowest probability of collision corresponds to a label without a collision. Loss is an L2 loss computed by summing the squared differences between labels and probabilities of collision for each action. As seen in figure 1, the training accuracy roughly always increased with the number of iterations. The validation accuracy initially increased until about 150 epochs. After about 150 epochs, the validation accuracy stayed roughly constant.

##### Neural Network Accuracy

<center><img src="assets/images/Accuracy_0.1.png" width="300" ></center>
<center>*Figure 10: The training and validation accuracy of a neural network while training. The validation accuracy stops increasing after approximately 150 epochs, indicating overfitting.*</center>

As seen in figure 10, the training loss roughly always decreased with the number of iterations. The validation loss decreased until about 150 epochs, after which the validation loss stopped decreasing. The neural network was overfitting to the training set after about 150 epochs, after which the training loss continued decreasing while the validation loss did not decrease.

##### Neural Network Loss

<center><img src="assets/images/Loss_0.1.png" width="300" ></center>
<center>*Figure 11: The training and validation loss of a neural network while training. The validation loss stops decreasing after approximately 150 epochs, indicating overfitting.*</center>

### Results - [Insert Author]

##### Visualization of Robot in Hallway

<center><img src="assets/images/RosbagRvizActions.gif" width="500" ></center>
<center>*Figure 12: A video of the robot navigating a zig-zag hallway using a neural network to select the steering angle from only camera data. Laser scan data from a real trial of the robot was used to localize the robot in a known map to produce this visualization. The green arrow represents the pose of the robot inferred from localization. The green curve represents the car's selected steering angle arc for a length of 1.5 m. The white dots represent laser scans projected from the inferred pose used to localize the robot.*</center>


##### Steering Angle vs. Time

<center><img src="assets/images/SteeringAngle.png" width="300" ></center>
<center>*Figure 2: The steering angle of the robot over time over three trials of the robot in a zig-zag hallway. The starting position of the robot in the three runs differs within a 1 meter radius. The steering angle is time averaged over a period of 2 seconds to better illustrate commonalities between the trials. The robot's steering angle decreases sharply around 10-15 seconds, and increases sharply between 15-20 seconds in all three trials.*</center>

##### Wall Distance vs. Time

<center><img src="assets/images/SteeringAngle.png" width="300" ></center>
<center>*Figure 13: The robot's distance to the wall over time over three trials of the robot in a zig-zag hallway. The distance to the wall was found by using laser scans to localize the robot in a known map. The starting position of the robot in the three runs differs within a 1 meter radius. The distance to the wall averages approximately 0.8 m in all three trials, with an average minimum distance to wall of approximately 0.4 m.*</center>

## Lessons Learned - [Insert Author]

Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nam sed consequat ligula. Aliquam erat volutpat. Cras iaculis diam vitae nunc ultricies, et egestas lorem eleifend. Ut sit amet leo vitae libero maximus molestie non ac nunc. Ut ac mi ante. Vivamus convallis convallis neque, sit amet sollicitudin arcu bibendum sit amet. Phasellus finibus dolor vitae leo cursus, eu lobortis nisl blandit. Quisque tincidunt et nisi a hendrerit. Sed et nunc quis neque egestas sollicitudin. Curabitur auctor bibendum odio. Proin aliquam cursus metus, at fermentum tellus luctus vel. Morbi ut mi id augue lacinia faucibus.

> Duis vel nunc sit amet risus consectetur dictum. Nulla mollis varius erat, vitae gravida est elementum a. Curabitur velit sapien, placerat ac scelerisque quis, ultricies at sem. Maecenas ut elit congue, condimentum lacus eu, scelerisque nunc. Curabitur mattis velit vitae sem placerat varius vel euismod leo. Nulla tempus tempor sollicitudin. Sed id tortor vestibulum, tincidunt lorem a, suscipit lacus. Mauris vitae pretium libero, at dapibus massa. Curabitur eleifend bibendum pharetra. Nullam gravida viverra lacus eu blandit. Praesent nec odio ut magna scelerisque vulputate. Sed in libero porta, imperdiet magna maximus, efficitur urna.

### Technical Conclusions - Akhilan Boopathy

The final project allowed the team to understand the theoretical and practical considerations behind end to end training and usage of deep neural networks for controlling a robot. The team found that accurately labeling the image data was necessary so that the neural network could find structure in the training data and generalize to the test data. Finding ways to validate the data labels before training was a useful way to ensure the quality of the data passed to the neural network. The team also found that the neural network was prone to overfitting with too little or poorly labeled data.

### CI Conclusions - Akhilan Boopathy, Shannon Hwang, Shiloh Curtis

1. The team found that planning for delays and uncertainty in when tasks would be accomplished allowed team members to be more productive. While some team members were waiting for certain tasks to be completed, they could plan ahead and work on future tasks. The team found that even when it was not possible to fully test certain code without having completed an earlier piece of the pipeline, it was still possible to verify it in limited ways.
2. The team found that careful specification of architecture and methodology was critical to parallelizing code writing efficiently. 
3. The team learned that when progress is behind schedule, it's particularly important to provide frequent and detailed status updates, both so the team has an opportunity to replan and so other members of the team can provide any ideas they might have about the issue.  
