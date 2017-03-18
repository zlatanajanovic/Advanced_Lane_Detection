##Writeup

---

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

[image1]: ./output_images/distorted.png "Distorted"
[image2]: ./output_images/02_corner_detection.png "Corner detection"
[image3]: ./output_images/03_undistorted.png "Undistorted image"
[image4]: ./output_images/04_lane.png "Example of image disorted and undisorted"
[image5]: ./output_images/05_mag_thresh.png "Lane detection based on magnitude of gradient in X direction"
[image6]: ./output_images/06_col_thresh.png "Lane detection based on color"
[image7]: ./output_images/07_combined.png "Combined detection"
[image8]: ./output_images/08_perspective_mark.png "Perspective mark"
[image9]: ./output_images/09_perspective_transf.png "Perspective transformatio to Birdview"
[image10]: ./output_images/10_sliding_window.png "Sliding window"
[image11]: ./output_images/11_vicinity_search.png "Search in vicinity previous lane finding"
[image12]: ./output_images/12_processed_image.png "Output"
[video1]: ./project_marked.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points
###Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
###Writeup / README

####1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.

You're reading it!
###Camera Calibration

####1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the first code cell of the IPython notebook located in "./main.ipynb" 

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates of regular grid, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

![alt text][image2]

The code for this step is contained in the third code cell of the IPython notebook located in "./main.ipynb" 

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![alt text][image3]

The code for this step is contained in the fourth code cell of the IPython notebook located in "./main.ipynb" 

###Pipeline (single images)

####1. Provide an example of a distortion-corrected image.
To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:
![alt text][image4]

I used camera calibration and distortion coefficients calculated during calibration and I applied this distortion correction to the test image using the `cv2.undistort()` function.

The code for this step is contained in the fifth code cell of the IPython notebook located in "./main.ipynb" 

####2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result. 

The code for this step is contained in the 6th, 7th, and 8th code cell of the IPython notebook located in "./main.ipynb"

Gradient magnitude is determined in x direction because it better detects lane lines. Here's an example of my output for this step.

![alt text][image5]

For color selection, HLS coding is used. Both H and S channels are used. Thresholding low H levels detects lane good as well as thresholding high S levels. But combining them with and function removes other non-lane parts. Here's an example of my output for this step.

![alt text][image6]

Then I used combination of color and gradient thresholds to generate a binary image with or function. Here's an example of my output for this step.

![alt text][image7]

####3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform includes a function called `birdview(img_persp)`, which appears in the 10th code cell of the IPython notebook located in "./main.ipynb".  The `birdview(img_persp)` function takes as inputs an image (`img_persp`).  I chose the hardcode the source and destination points in the following manner:

Source points are choosen from straight lane image with ploting line on a image like on a following image. Destination points are chosen to be vertical.

![alt text][image8]


This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 595, 450      | 400, 0        | 
| 250, 687      | 400, 720      |
| 685, 450      | 880, 720      |
| 1055,687      | 880, 0        |

Here's an example of my output for this step.

![alt text][image9]

####4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

Then I did some other stuff and fit my lane lines with a 2nd order polynomial kinda like this:
I used histogram on lower half of the image to find origin of the lane and sliding window to find rectangles and than fit 2nd orger polynomial to these values.

Code for this appears in the 11th and 12th code cell of the IPython notebook located in "./main.ipynb".

Here's an example of my output for this step.

![alt text][image10]

Once lanes are found we can search only in vicinity of found lane. This was used only for testing, I didn't use this in processing video.

Code for this appears in the 13th and 14th code cell of the IPython notebook located in "./main.ipynb".

Here's an example of my output for this step.
![alt text][image11]

####5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

Code for this appears in the 15th code cell of the IPython notebook located in "./main.ipynb".

Beside this I also calculated position of a vehicle based on position of the middle of the lane on a image.

Code for this appears in the 16th code cell of the IPython notebook located in "./main.ipynb".

####6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

Code for this appears in the 17th and 18th code cell of the IPython notebook located in "./main.ipynb".
Here is an example of my result on a test image:

![alt text][image12]

---

###Pipeline (video)

####1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video.

Here's a [link to my video result](./project_marked.mp4)

---

###Discussion

####1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

My pipeline didn't have any problems on reference video for project. It fails on challenge videos because there is not any filtering of lane estimation and their validation. The first step should be to do sanity check and remove outliers and than do some averaging, and maybe even Kalman filter for lane position estimation.


