## Project: Search and Sample Return

---

**The goals / steps of this project are the following:**  

**Training / Calibration**  

* Download the simulator and take data in "Training Mode"
* Test out the functions in the Jupyter Notebook provided
* Add functions to detect obstacles and samples of interest (golden rocks)
* Fill in the `process_image()` function with the appropriate image processing steps (perspective transform, color threshold etc.) to get from raw images to a map.  The `output_image` you create in this step should demonstrate that your mapping pipeline works.
* Use `moviepy` to process the images in your saved dataset with the `process_image()` function.  Include the video you produce as part of your submission.

**Autonomous Navigation / Mapping**

* Fill in the `perception_step()` function within the `perception.py` script with the appropriate image processing functions to create a map and update `Rover()` data (similar to what you did with `process_image()` in the notebook). 
* Fill in the `decision_step()` function within the `decision.py` script with conditional statements that take into consideration the outputs of the `perception_step()` in deciding how to issue throttle, brake and steering commands. 
* Iterate on your perception and decision function until your rover does a reasonable (need to define metric) job of navigating and mapping.  

## [Rubric](https://review.udacity.com/#!/rubrics/916/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  

You're reading it!

### Notebook Analysis
#### 1. Run the functions provided in the notebook on test images (first with the test data provided, next on data you have recorded). Add/modify functions to allow for color selection of obstacles and rock samples.

The threshold values for detection have been added to the `process_image()` function. The navigatable terrain detetion works quite well with the default values but has been tested and improved to RGB (170,160,150). The obstacles are just the inversion of the navigatable terrain pixels.

For the rock detection the function color_thresh was updated with max values for the color in order to filter yellow. As yellow does not or only contain a little blue a max value functionality in `color_thresh()` makes sense. The color theroshold is set to (120,120,0) and the max to (255,255,50) in order to properly detect yellow.

#### 1. Populate the `process_image()` function with the appropriate analysis steps to map pixels identifying navigable terrain, obstacles and rock samples into a worldmap.  Run `process_image()` on your test data using the `moviepy` functions provided to create video output of your result. 

First the image from the camera is trasnsformed to a view from above. The assumption is a flat world with a pitch and roll of zero for the camera. The terrain, obstacle and rock detection works as described above based on color thresholds.

The given pixels are then mapped into two views. The rover-centric view and the absolute view, the worldmap. The rover centric view is used for the navigation. In order to facilitate the navigation the pixels are provided in polar coordinates where the rover is at zero. The mapping to the absolute frame is done by translation and rotation based on the knowledge of the actual position and the yaw angle. To increas the accuracy of the absolute mapping the data is only updated if the rover is within a certain pitch and roll angle. 

### Autonomous Navigation and Mapping

#### 1. Fill in the `perception_step()` (at the bottom of the `perception.py` script) and `decision_step()` (in `decision.py`) functions in the autonomous mapping scripts and an explanation is provided in the writeup of how and why these functions were modified as they were.

The preception is done as before just using the Rover class as data source.

The `decision_step()` was only slightly modified. I didn't like to have the average gradient over the whole range. So I limited the distance and split the view in a left, centre and right sector. The sector with the most navigateable pixels will be used to calculate the average angle for steering. This makes the steering a bit smoother and does not react to what happens faraway.

![alt text](./Three%20sector%20navigation.png)

#### 2. Launching in autonomous mode your rover can navigate and map autonomously.  Explain your results and how you might improve them in your writeup.  

The results a reasonable mapping and rock detection work as expected. But there is a lot of room for improvement. The decision tree could be made much more detailed in order to cover some major problems I encountered during the tests.

* Infinite loop detection (going in circles)
* Getting stuck on big rocks because the pixels on the left or right still indicate some room
* Complete mapping with random decisions to go left or rigth (which was prepared with the sectors)



