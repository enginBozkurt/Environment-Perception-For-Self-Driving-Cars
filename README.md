# Environment-Perception-For-Self-Driving-Cars
Visual Perception Stack For Self-Driving Cars 


## General overview and goals of the project

- Using the output of semantic segmentation neural networks to implement drivable space estimation in 3D.
- Using the output of semantic segmentation neural networks to implement lane estimation.
- Using the output of semantic segmentation to filter errors in the output of 2D object detectors.
- Using the filtered 2D object detection results to determine how far obstacles are from the self-driving car.

# Some implementation details and tricks

![1](https://user-images.githubusercontent.com/30608533/64910373-4ea5f300-d71e-11e9-9715-88c9318a0221.jpg)


- All the required 3D estimation will be performed in the camera coordinate frame
- The camera as a sensor is usually oriented with its y-axis pointing downward.
- The only thing you will need to be careful about, is the sign of the height value of pixels. 
- Points higher than the camera will have a negative height, while points lower than the camera will have a positive height. 

![2](https://user-images.githubusercontent.com/30608533/64910374-52d21080-d71e-11e9-94ef-4419708de081.jpg)


![3](https://user-images.githubusercontent.com/30608533/64910377-582f5b00-d71e-11e9-95eb-1ed8ed0d823c.jpg)

- To filter out the horizontal lines, we can rely on the slope of the estimated lines. 
- Horizontal lines and images tend to have a slope very close to zero. However, we also want to remove heavily slanted lines. 
- As such, a threshold is introduced as a lower limit of allowed slopes for the output of this filtering step. The exact value of this threshold needs to be determined empirically, try values between 0.1 and 0.3 for best results.


![4](https://user-images.githubusercontent.com/30608533/64910416-ec012700-d71e-11e9-837a-94d3c6b7c2a8.jpg)

- A simple clustering algorithm would be to first choose a cluster center at random from the remaining filter lines, then add to the cluster any lines that have a similar slope or intercept to the cluster line. 
- A line is considered close to the cluster center if the difference between its slope and slope of the cluster center is less than a specific slope threshold, and the distance between its intercept and the intercept of the cluster center is less than a specific intercept threshold as well. 
- The slope difference threshold is usually chosen to be a maximum of 0.3, while the intercept difference threshold is defined in pixels, and is usually chosen between 20 and 50 pixels.

![5](https://user-images.githubusercontent.com/30608533/64910417-eefc1780-d71e-11e9-8b4f-81e4205276e9.jpg)


![6](https://user-images.githubusercontent.com/30608533/64910420-f28f9e80-d71e-11e9-9ffd-185dcf9f1383.jpg)


![7](https://user-images.githubusercontent.com/30608533/64910424-02a77e00-d71f-11e9-9dcb-1dccd23dc9ae.jpg)

- The output of object detection is usually reliable. But for this assessment, we are given a high recall low precision detector that detects all objects in the scene, but also provides some false positives. 
- We are required to use the output from semantic segmentation to eliminate these false positives before estimating the distance to the obstacles. 
- The results should be bounding boxes that reliably contain obstacles. To perform this filtering, we will need to use the semantic segmentation output to count the number of pixels in the bounding box that have the same category as the classification output from the 2D object detector. 
- The trick here, is that this number will depend on the size of the bounding box. We will need to normalize the pixel count by the area of the bounding box before attempting to filter out the detections with a threshold. The final normalized count is equivalent to computing the area inside the bounding box occupied by pixels belonging to the correct category. 

## Final Notes:
This is the final assignment project from Visual Perception for Self-Driving Cars course of Self-Driving Cars Specialization by University of Toronto.
