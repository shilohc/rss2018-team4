Final Project
=====

## Overview and Motivations - [Insert Author]

### Hypothesis/Goal -

## Proposed Approach - [Insert Author]

Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nam sed consequat ligula. Aliquam erat volutpat. Cras iaculis diam vitae nunc ultricies, et egestas lorem eleifend. Ut sit amet leo vitae libero maximus molestie non ac nunc. Ut ac mi ante. Vivamus convallis convallis neque, sit amet sollicitudin arcu bibendum sit amet. Phasellus finibus dolor vitae leo cursus, eu lobortis nisl blandit. Quisque tincidunt et nisi a hendrerit. Sed et nunc quis neque egestas sollicitudin. Curabitur auctor bibendum odio. Proin aliquam cursus metus, at fermentum tellus luctus vel. Morbi ut mi id augue lacinia faucibus.

### Initial Setup - [Insert Author]
#### Division of Work/Pipeline -

### Technical Approach - [Insert Author]

#### Image Labeling -

#### Neural Network Architecture -

#### Neural Network Training - Akhilan Boopathy

The neural network was trained using a subset of the labeled images. The data was split into a training and validation set, with 95% of the data randomly selected as the training set. For each epoch of training, the training set was split into batches of size 1000, and the network was trained with each batch. Training was performed using stochastic gradient descent with a learning rate of 0.01 and momentum of 0.9. After each epoch of training, which constitutes one full pass through the training set, the loss and accuracy on the validation set were evaluated. Training was performed for sets of 1000 epochs each, until validation loss started increasing, indicating overfitting.

### ROS Implementation - [Insert Author]

#### Image Labeling -

## Experimental Evaluation - [Insert Author]

Nulla tempus tempor sollicitudin. Sed id tortor vestibulum, tincidunt lorem a, suscipit lacus. Mauris vitae pretium libero, at dapibus massa. Curabitur eleifend bibendum pharetra. Nullam gravida viverra lacus eu blandit. Praesent nec odio ut magna scelerisque vulputate. Sed in libero porta, imperdiet magna maximus, efficitur urna.

### Testing Procedure - [Insert Author]

#### Image Labeling -

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

### Technical Conclusions - [Insert Author]

Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nam sed consequat ligula. Aliquam erat volutpat. Cras iaculis diam vitae nunc ultricies, et egestas lorem eleifend. Ut sit amet leo vitae libero maximus molestie non ac nunc. Ut ac mi ante. Vivamus convallis convallis neque, sit amet sollicitudin arcu bibendum sit amet. Phasellus finibus dolor vitae leo cursus, eu lobortis nisl blandit. Quisque tincidunt et nisi a hendrerit. Sed et nunc quis neque egestas sollicitudin. Curabitur auctor bibendum odio. Proin aliquam cursus metus, at fermentum tellus luctus vel. Morbi ut mi id augue lacinia faucibus.

Duis vel nunc sit amet risus consectetur dictum. Nulla mollis varius erat, vitae gravida est elementum a. Curabitur velit sapien, placerat ac scelerisque quis, ultricies at sem. Maecenas ut elit congue, condimentum lacus eu, scelerisque nunc. Curabitur mattis velit vitae sem placerat varius vel euismod leo. Cras quis elit quam. Proin scelerisque lobortis erat, eu euismod ex mattis ac. Curabitur non felis mauris. Integer mauris nisi, rutrum id finibus vel, ornare quis diam. Cras lectus nisi, pharetra ut elit at, porta auctor ex. Cras lobortis nisl leo, varius aliquet arcu sollicitudin vel. Aliquam quis nulla sapien. Donec porttitor, tortor vel iaculis vehicula, ipsum eros dictum eros, sit amet tempor orci felis vitae magna. Sed velit lacus, tincidunt sit amet quam sed, aliquam porttitor ex.

### CI Conclusions - Akhilan Boopathy, 

1. The team found that planning for delays and uncertainty in when tasks would be accomplished allowed team members to be more productive. While some team members were waiting for certain tasks to be completed, they could plan ahead and work on future tasks. The team found that even when it was not possible to fully test certain code without having completed an earlier piece of the pipeline, it was still possible to verify it in limited ways.
2. Student 2
3. Student 3
