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

[//]: # (Image References)

[image1]: ./writeup/example_rock.png
[image2]: ./writeup/example_rock_masked.png
[image3]: ./writeup/all_functions_applied.png
[image4]: ./output/test_mapping.mp4

## [Rubric](https://review.udacity.com/#!/rubrics/916/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  

You're reading it!

### Notebook Analysis
#### 1. Run the functions provided in the notebook on test images (first with the test data provided, next on data you have recorded). Add/modify functions to allow for color selection of obstacles and rock samples.
The first image shows the calibration picture of the grid, a rock and some obstacles. The obstacles picture is recorded by myself, rock and grid is from the calibration images

![alt text][image1]

The second image shows the same pictures, after applying functions. The grid has been applied a color threshold, the rock has been masked with hsv-values and the obstacle has been applied a color threshold and a perspective transform.
![alt text][image2]

This set of images shows the application of all functions with a random image from my recordings:
![alt text][image3]


#### 1. Populate the `process_image()` function with the appropriate analysis steps to map pixels identifying navigable terrain, obstacles and rock samples into a worldmap.  Run `process_image()` on your test data using the `moviepy` functions provided to create video output of your result. 

![alt text][image4]

### Autonomous Navigation and Mapping

#### 1. Fill in the `perception_step()` (at the bottom of the `perception.py` script) and `decision_step()` (in `decision.py`) functions in the autonomous mapping scripts and an explanation is provided in the writeup of how and why these functions were modified as they were.

I filled in perception.py with the functions from the lecture. Additionally, I implemented obstacles_thresh and mask_rock.

mask_rock defines a hsv boundary for the yellow color of the rocks, converts the RGB Image to HSV and then uses the open-cv inRange function to threshold the rock.
obstacles_thresh works similarly to color_thresh from the lecture, with the slight difference that if saves all colors below the rgb-threshold. This way I get one image with all navigatable terrain from color_thresh, and one image with all not-navigatable-terrain/obstacles from obstacles_thresh. (in the jupyter notebook the function is named mask_obs instead of obstacles_thresh).

In decisions.py I added a 'stuck' status for Rover mode to detect if the Rover is using throttle but not moving (in case it drives against a rock). I mostly stayed with the provided sample code (see below why)


#### 2. Launching in autonomous mode your rover can navigate and map autonomously.  Explain your results and how you might improve them in your writeup.  

**Note: running the simulator with different choices of resolution and graphics quality may produce different results, particularly on different machines!  Make a note of your simulator settings (resolution and graphics quality set on launch) and frames per second (FPS output to terminal by `drive_rover.py`) in your writeup when you submit the project so your reviewer can reproduce your results.**

#### Settings

- FPS: 15
- Graphics: 1600x1200
- Graphics Quality: Fastest
- System: Ubuntu Linux

The techniques I used is mostly staying with the provided sample code and using the mean angle to drive through the terrain. I additionally added a stuck status, in case the Rover was driving against a wall. In that case, throttle is set to -0.2 so the rover drives a bit backwards and then set back to forward status to drive again.

I have a bunch of ideas on how to fine-tune this project, but with the deadline approaching I wasn't able to implement them. The Rover achived the MVB with 40% Map, 60% fidelity and 1 found sample, even If it took a while.

First I was fighting with internationalization problems in the simulator ( having a , instead of a. as a number-delimiter on my system). Then I am a bit weak on python and had trouble understanding the numpi-functions.
At last, I am still having the issue that my rover only sends either -15 or +15 to the steering, regardless which values I save to it. Live Support wasn't able to reproduce the Issue, and the slack-channel didn't have an idea either. This made testing really hard and your test-results might differ because of this.

I tried to make the Rover a wall-climber by taking the maximum angle instead of the mean and applying an offset. The Rover started driving along a wall which was a success, but it got stuck a lot and map fidelity/coverage got down, so I went back to mean angles.

For collecting the rocks (which I am unable to do for now) I calculated the rovers stopping distance. I recorded the sim with a screencapture-program and took the rover to fullspeed and fullstop again.
Then, I extracted the positions when going fullspeed and when velocity was zero again:
Position at full speed: 107,2 x, 62,1 y
Position at full stop:  108,9 x, 57,3 y

I then calculated the braking distance: sqrt( (108,9 - 107,2) xx 2 + ( 57,3 - 62,1 ) xx 2 ) = 5,1 (rounded)
With braking distance known and initial velocity known I calculated the braking delay:  braking_distance = initial_velocity xx2 / 2 x braking_delay  => 5,1 = 25 / 2 x a   => a = 1,02

My idea was the following: When a rock is detected, I know the distance between the Rover and the rock and knowing the braking_delay I could calculate a factor to slowly pull down the throttle and gently come to a halt before the rock. Due to time constraints, I didn't make it further then the calculations.

I enjoyed this first project even with the simulator issues and would have liked to try a bit more, but unfortunately I am already 30 minutes behind the submission deadline.


Regards,
Sandra






