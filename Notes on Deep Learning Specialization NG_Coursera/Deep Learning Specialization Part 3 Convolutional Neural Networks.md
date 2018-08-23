# Deep Learning Specialization: Part 3 Convolutional Neural Networks

## Week 2

**Look at case studies**

Classic networks

- LeNet 5
- AlexNet
- VGG

ResNet

- 152 Layer network

Inception network

**Classic networks**

LeNet - 5 - More average pooling that max pooling as today. Last wo layers fully connected. 60k parameters. Height/width go down. Number of channels go up. Conv - pool - conv pool fc fc output.

AlexNet - Much bigger than LeNet (60 million parameters) - Used Relu Activation - Complicated gpu communication - Local Response Normalization (LRN) - Seminal Paper for Deep Learning for Computer Vision

VGG - 16 - Just two types of layers: CONV 3x3, stride=1 , same and Max-Pool = 2x2, stride =2 (138 millionen parameters) - VGG 19 (even bigger network) - height/with go down, channels go up systematically.

**ResNets**

Residual blocks - shortcut/skip connection - Plain versus Residual network - Apply shorcuts to a plain network - In reality plain deep networks trainings error increases - This problem is attacked by ResNet.

**Why ResNets Work**

Idenity function is easy to learn for a residual block - Adding these doesnt hurt performance - ??

**Networks in Networks and 1x1 Convolutions**

Shrinking #channels - 

**Inception Network Motivation**

**Inception Network**

**Using Open-Source Implementation**

**Transer Learning**

Use Open Source weights - Freeze first layers.

**Data Augmentation**

Mirroring - Random Cropping - Rotation/Shearing/Local warping - Colour shifting/augmentation with PCA - 

**State of computer vision**

Data vs. hand-engineering - Two sources of knowledge: labeled data, hand-engineering features/network architecture, other components - Tips for benchmarks: Ensembling (Train several networks and average their output), Multi-crop at test time (Run classifiert on multiple version of test images and average results.) - Use open source code.





