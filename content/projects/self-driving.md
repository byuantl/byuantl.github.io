---
title: "Simulated Self-driving Agent"
weight: 1
# bookFlatSection: false
# bookToc: true
# bookHidden: true
# bookCollapseSection: false
# bookComments: false
# bookSearchExclude: false
# bookHref: ''
# bookIcon: ''
---
{{< katex />}}
# **Simulated Self-driving Agent**
{{< image 
  src="/projects/self-driving/pedestrian_detection.png" 
  alt="Simulated Self-driving Agent" 
  loading="lazy" 
>}}
# **Overview**
Self-driving agent navigating a simulated ROS Gazebo environment built with a computer vision stack including a custom CNN for OCR and sign recognition.

# **Highlights**
- Project Management + Advanced Problem Solving
- ROS (Robot Operating System) + Gazebo Simulation 
- Data Classification and Dataset Generation (Python)
- Machine Learning (Deep Learning, Convolutional Neural Networks) with TensorFLow and Keras

## **Course**
- Learned how to use ROS and Gazebo
  - configured .urdf (unified robot descriptor format) to interact with physics simulation
- Built a simple neural network from scratch, trained with gradient descent.
- Assembled a character dataset and trained a CNN to recognize license plate characters
- Taught a robot how to follow a line with Q-Learning (reinforcement learning)
- Taught a cart how to balance a pole with Deep Q-Learning

## **Competition**
- Implemented specific masking and filtering algorithms to obtain road lines from a variety of terrain.
- Added motion and object detection to understand recognize when it is safe for the robot to cross the road
- Created a network of ROS nodes to read incoming camera data, track robot state, run CNN inference, and publish to a score tracker.
- Generated thousands of character samples with a high degree of augmentation to train a robust letter-recognition CNN
- Utilized homography and perspective transforms to obtain sign photos for inference.
- Built a debugging GUI in PyQt5 to view all robot sign captures, different camera frames, etc.

---
# **Details**
Check out the project repository on [Github](https://github.com/Taichi-Kamei/ENPH353_Controller)!
{{< pdf src="/projects/self-driving/Bowen_Yuan_Self-driving_Report.pdf#view=Fit&page=2" height="85vh" >}}
