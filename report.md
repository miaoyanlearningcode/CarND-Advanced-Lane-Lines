**Advanced Lane Finding Project**

The goals / steps of this project are the following:

* Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
* Apply a distortion correction to raw images.
* Use color transforms, gradients, etc., to create a thresholded binary image.
* Apply a perspective transform to rectify binary image ("birds-eye view").
* Detect lane pixels and fit to find the lane boundary.
* Determine the curvature of the lane and vehicle position with respect to center.
* Warp the detected lane boundaries back onto the original image.
* Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.

[//]: # (Image References)

[image1]: ./examples/undistort_output.png "Undistorted"
[image2]: ./output_images/img4.png "Road"
[image3]: ./output_images/undist_img4.png "undistorted"
[image4]: ./output_images/binary_img4.png "Binary Example"
[image5]: ./output_images/img1.png "original Example"
[image6]: ./output_images/perspective_img1.png "perspective"
[image7]: ./output_images/binaryPerspective_img1.png "binaryLine"
[image8]: ./output_images/line.png "line"
[image9]: ./output_images/line_img1.png "final"
[video1]: ./project_video.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points
###Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
###Writeup / README

####1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Advanced-Lane-Lines/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!
###Camera Calibration

####1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the first code cell of the IPython notebook located in "./advanced_laneDetection.ipynb".  

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![alt text][image1]

###Pipeline (single images)

####1. Provide an example of a distortion-corrected image.
To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:
![alt text][image2]
![alt_text][image3]
####2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.
I used a combination of color and gradient thresholds to generate a binary image. Gradient thresholds methods include absolute gradient threshold in x and y direction, magnitude gradient threshold and direction gradient threshold.  Here's an example of my output for this step.  

![alt text][image4]

####3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform is in the 8th code cell of the IPython notebook `advanced_laneDetection`.  In this cell,  set up source (`src`) points and destination (`dst`) points.  I chose the hardcode the source and destination points in the following manner:

```
nx = 1280
ny = 720
xOffset = 20
yOffset = 20

src = np.float32(
	[[580,450],
    [710,450],
    [1200,700],
    [100,700]])

dst = np.float32(
	[[xOffset,yOffset],
    [ny-xOffset,yOffset],
    [ny-xOffset, nx],
    [xOffset,nx]])

```
This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 580, 450      | 20, 20        | 
| 710, 450      | 700, 20      |
| 1200, 700     | 700, 1280      |
| 100, 700      | 20, 1280        |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][image5]
![alt_text][image6]
####4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

The sequence I did is to do the gradient combination to get the binary image first and then do the perspective transoformation.This is the result:

![alt text][image7]

Then I use slide windows to find the peaks of each windows and store the data in `leftx`, `lefty` and `rightx`, `righty`. 

Then apply the function `np.polyfit` to fit the left line and right line. The parameters are stored in `left_fix` and `right_fit`. 

In last step, `ploty` is the uniform sample in y direction and `left_fix`, `right_fix` are the values returned from the polynoimals of left and right at `ploty`
Here is the result of this step:

![alt_text][image8]

The yellow lines are the lane marking we found in bird angle. 




####5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I did this in lines # through # in my code in `my_other_file.py`

####6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step in lines # through # in my code in `yet_another_file.py` in the function `map_lane()`.  
Last step, apply inverse matrix to transfer bird angle image to the original image and draw lines on it. Here is the result:

![alt_text][image9]

---

###Pipeline (video)

####1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./project_video.mp4)

---

###Discussion

####1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

Here I'll talk about the approach I took, what techniques I used, what worked and why, where the pipeline might fail and how I might improve it if I were going to pursue this project further.  

