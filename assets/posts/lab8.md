Final Project
=====

## Overview and Motivations - Shannon Hwang

Throughout the course, planning and control algorithms were implemented to autonomously drive a robotic RC car; however, they were largely implemented under the assumption that a LIDAR capable of accurately determining distances to obstacles was available. However, lidars are expensive and relatively inaccessible; it would be optimal to devise some sort of navigation policy capable of directing the robot given only images from relatively inexpensive cameras. Thus, our hypothesis for this lab was as follows:

### Hypothesis/Goal - A deep neural network can be trained such that it is capable of autonomously driving a robot through an unknown environment using only camera images. 

## Proposed Approach - [Insert Author]

Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nam sed consequat ligula. Aliquam erat volutpat. Cras iaculis diam vitae nunc ultricies, et egestas lorem eleifend. Ut sit amet leo vitae libero maximus molestie non ac nunc. Ut ac mi ante. Vivamus convallis convallis neque, sit amet sollicitudin arcu bibendum sit amet. Phasellus finibus dolor vitae leo cursus, eu lobortis nisl blandit. Quisque tincidunt et nisi a hendrerit. Sed et nunc quis neque egestas sollicitudin. Curabitur auctor bibendum odio. Proin aliquam cursus metus, at fermentum tellus luctus vel. Morbi ut mi id augue lacinia faucibus.

### Initial Setup - [Insert Author]
#### Division of Work/Pipeline - Eleanor Pence

The first step in the process of training the DNN to drive with camera input only was to design the labeling algorithm. This work was done during an all-group brainstorming session in lab. Once a labeling method had been decided on, Shiloh and Shannon began the implementation of the labeling algorithm, while Eleanor and Akhilan worked on prototyping the DNN and writing scripts for training and inference. 

After that, the team entered a phase in which the labeling scheme and training were repeatedly refined. In each case, Shiloh would typically make modifications to the labeling code, and a team member would re-label the data. This resulted in a pickled version of several numpy arrays, which included the newly labeled data, being added to the git repository, which Akhilan would use to re-train the DNN. Real-world testing and further discussion with the whole team in the Stata center basement would typically yield further potential ideas for improvements, which would then be implemented in much the same way before, until desired performance was achieved.  

### Technical Approach - [Insert Author]

#### Image Labeling - Shiloh Curtis

Images were labeled with actions (driving commands) and the minimum distance between any point on the trajectory resulting from that action and an obstacle (based on the collected scan data).  Further processing using distance thresholds then added booleans representing whether the robot was expected to collide with an obstacle as a result of that action.  

The action definition consists of a steering angle; the speed and distance along the trajectory are pre-defined, so this completely defines a simple trajectory.  Images were then labeled by sampling points from the trajectory and finding the minimum distance to any obstacle, then taking the overall minimum distance along the trajectory.  Robot size was accounted for in the postprocessing step, which models the robot as a circle and checks if the obstacle distance threshold is larger than the robot "radius".  

#### Image Preprocessing - Eleanor Pence
Before they could be used in either training or real-time inference, images underwent a preprocessing step. Images were resized to 45x80 and transformed into grayscale, and then the image data was normalized.  Resizing of the images improved the speed and memory usage of the network at inference time, but during training, it also allowed the entire dataset of approximately 20,000 images to be loaded into memory at once, which made training much simpler.  

#### Neural Network Architecture - Eleanor Pence

The neural network architecture we used was relatively simple. The layers of the network were fully connected; the first three layers had 300 units each and used rectified linear units (ReLU) as an activation function, and the last layer, a "logits" layer that gave the actual classifications, had 20 units (equivalent to the number of actions the robot could take), and used a sigmoid activation function. 

#### Neural Network Training - Akhilan Boopathy

The neural network was trained using a subset of the labeled images. The data was split into a training and validation set, with 95% of the data randomly selected as the training set. For each epoch of training, the training set was split into batches of size 1000, and the network was trained with each batch. Training was performed using stochastic gradient descent with a learning rate of 0.01 and momentum of 0.9. After each epoch of training, which constitutes one full pass through the training set, the loss and accuracy on the validation set were evaluated. Training was performed for sets of 1000 epochs each, until validation loss started increasing, indicating overfitting.

### ROS Implementation - [Insert Author]

#### Image Labeling - Shiloh Curtis

The training dataset consisted initially of a collection of bagfiles containing image and lidar data.  Data were collected from these bagfiles using the rosbag API; the lidar data was used to determine which trajectories were safe for the robot to traverse, and thereby label the images.  

#### Driving Using NN Output - [Insert Author]

## Experimental Evaluation - [Insert Author]

Nulla tempus tempor sollicitudin. Sed id tortor vestibulum, tincidunt lorem a, suscipit lacus. Mauris vitae pretium libero, at dapibus massa. Curabitur eleifend bibendum pharetra. Nullam gravida viverra lacus eu blandit. Praesent nec odio ut magna scelerisque vulputate. Sed in libero porta, imperdiet magna maximus, efficitur urna.

### Testing Procedure - [Insert Author]

#### Image Labeling - Shannon Hwang

Images were transformed to be 45x80 grayscale images; this was verified by subscribing to the publisher feeding grayscale, resized images to the neural network and visualizing said images. 

#### Neural Network Verification - Akhilan Boopathy
The neural network was verified using a validation set before evaluating the neural network's performance on the robot. The neural network's performance was evaluated by computing the accuracy and loss values on a validation set. Accuracy is the fraction of the time that the action with the lowest probability of collision corresponds to a label without a collision. Loss is an L2 loss computed by summing the squared differences between labels and probabilities of collision for each action. As seen in figure 1, the training accuracy roughly always increased with the number of iterations. The validation accuracy initially increased until about 1000 epochs. After about 1000 epochs, the validation accuracy decreased.

##### Neural Network Accuracy

<center><img src="assets/images/Accuracy.png" width="300" ></center>
<center>*Figure 1: The training and validation accuracy of a neural network while training. The blue curve represents the training accuracy and the orange curve represents the validation accuracy.*</center>

As seen in figure 2, the training loss roughly always decreased with the number of iterations. The validation loss decreased until about 1000 epochs, after which the validation loss increased. The neural network was overfitting to the training set after about 1000 epochs, after which the training loss decreased dramatically while the validation loss did not decrease.

##### Neural Network Loss

<center><img src="assets/images/Loss.png" width="300" ></center>
<center>*Figure 2: The training and validation loss of a neural network while training. The blue curve represents the training loss and the orange curve represents the validation loss.*</center>

### Results - [Insert Author]

## Lessons Learned - [Insert Author]

Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nam sed consequat ligula. Aliquam erat volutpat. Cras iaculis diam vitae nunc ultricies, et egestas lorem eleifend. Ut sit amet leo vitae libero maximus molestie non ac nunc. Ut ac mi ante. Vivamus convallis convallis neque, sit amet sollicitudin arcu bibendum sit amet. Phasellus finibus dolor vitae leo cursus, eu lobortis nisl blandit. Quisque tincidunt et nisi a hendrerit. Sed et nunc quis neque egestas sollicitudin. Curabitur auctor bibendum odio. Proin aliquam cursus metus, at fermentum tellus luctus vel. Morbi ut mi id augue lacinia faucibus.

> Duis vel nunc sit amet risus consectetur dictum. Nulla mollis varius erat, vitae gravida est elementum a. Curabitur velit sapien, placerat ac scelerisque quis, ultricies at sem. Maecenas ut elit congue, condimentum lacus eu, scelerisque nunc. Curabitur mattis velit vitae sem placerat varius vel euismod leo. Nulla tempus tempor sollicitudin. Sed id tortor vestibulum, tincidunt lorem a, suscipit lacus. Mauris vitae pretium libero, at dapibus massa. Curabitur eleifend bibendum pharetra. Nullam gravida viverra lacus eu blandit. Praesent nec odio ut magna scelerisque vulputate. Sed in libero porta, imperdiet magna maximus, efficitur urna.

### Technical Conclusions - Akhilan Boopathy

The final project allowed the team to understand the theoretical and practical considerations behind end to end training and usage of deep neural networks for controlling a robot. The team found that accurately labeling the image data was necessary so that the neural network could find structure in the training data and generalize to the test data. Finding ways to validate the data labels before training was a useful way to ensure the quality of the data passed to the neural network. The team also found that the neural network was prone to overfitting with too little or poorly labeled data.

### CI Conclusions - Akhilan Boopathy, Shannon Hwang, Shiloh Curtis

1. The team found that planning for delays and uncertainty in when tasks would be accomplished allowed team members to be more productive. While some team members were waiting for certain tasks to be completed, they could plan ahead and work on future tasks. The team found that even when it was not possible to fully test certain code without having completed an earlier piece of the pipeline, it was still possible to verify it in limited ways.
2. The team found that careful specification of architecture and methodology was critical to parallelizing code writing efficiently. 
3. The team learned that when progress is behind schedule, it's particularly important to provide frequent and detailed status updates, both so the team has an opportunity to replan and so other members of the team can provide any ideas they might have about the issue.  
