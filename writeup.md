## Writeup 
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

[image1]: ./output_images/undistort_calibration1.jpg "Undistorted"
[image2]: ./output_images/undistort_test1.jpg "Road Transformed"
[image3]: ./output_images/threshold_test1.jpg "Binary Example"
[image4]: ./output_images/top_down_test1.jpg "Warp Example"
[image5]: ./output_images/sliding_windows_test1.jpg "Fit Visual"
[image7]: ./output_images/search_from_prior_test1.jpg "Fit line"
[image6]: ./output_images/test1.jpg "Output"
[video1]: ./output_images/project_video.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Camera Calibration

The code for this step is contained in the second and third code cell of the IPython notebook located in `./P2.ipynb` 

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  The `objpoints` and `imgpoints` are saved in a picke file for later use. I applied this distortion correction to the chessboard image using the `cv2.undistort()` function and obtained this result: 

![alt text][image1]

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

Using the same method above applied to the chessboard, a test image is unditorted:
![alt text][image2]


#### 2. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform can be found in `./P2.ipynb`. The source (`src`) and destination (`dst`) points are hardcoded and they are listed below:

This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 581, 477      | 384, 0        | 
| 699, 477      | 896, 0        |
| 896, 675      | 896, 720      |
| 384, 675      | 384, 720      |

The two functions are used in the process of perspective transoform: `cv2.getPerspectiveTransform` and `cv2.warpPerspective` I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][image4]

#### 3. Thresholding

I used a combination of color and gradient thresholds to generate a binary image (thresholding steps in section 4 of "./P2.ipynb").  `cv2.sobel` was used to threshold the image. The thresholding is a combination of sobel in x direction, y direction, the direction, and also the magnitude. After using `cv2.sobel`, the thresholding of s channel in the hls color is combined. Here's an example of my output for this step.

![alt text][image3]

#### 4. Fitting lane lines

Then I used the method of sliding windows to find pixels that can be used to fit lane lines. The code can be found in section 6.
I also used the method of search from prior to find pixels in later frames. 

![alt text][image5]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I did this "./P2.ipynb" with the function `np.polifit`. The code can be found in section 7.

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step in section 8 in my code in the function `display()` and `label()`.  Here is an example of my result on a test image:

![alt text][image6]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./project_video.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

The pipeline is working properly for the test images and the project video. However it fails on the challenge video; the reason is that it cannot filter out the irregular edges on the road. The pipeline tends to take those edges as lane lines. The way to improve the pipeline should be adding a method to compare newly-detected curvature to the previous values and elimitate drastic changes while keeping reasonable values.  
