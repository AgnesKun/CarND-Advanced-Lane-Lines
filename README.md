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

[image1]: ./camera_cal_output/calibration2.jpg "Calibration."
[image2.1]: ./test_images/test1.jpg "Original Image"
[image2.2]: ./output_images/test1_undist.jpg "Undistorted Image"
[image3]: ./output_images/test1_combo.jpg "Binary Example"
[image4]: ./output_images/test1_warp.jpg "Warp Example"
[image5]: ./output_images/test1_lane.jpg "Fit Visual"
[image6]: ./output_images/test1.jpg "Output"
[video1]: ./project_video.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the first code cell of the IPython notebook located in "./CameraCalibration.ipynb".  

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![alt text][image1]

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

I called to `cv2.undistort()` to undistort the image by using mtx, dist created in calibration.<p>
The original Image:
![alt text][image2.1]
The undistorted Image:
![alt_text][image2.2]

#### 2. Color transforms, gradients or other methods to create a thresholded binary image. 

I used a combination of color and gradient thresholds to generate a binary image (3rd code cell of the IPython notebook "./AdvancedLaneFindingImage.ipynb").  Here's an example of my output for this step.

![alt text][image3]

#### 3. Perspective transform and provide an example of a transformed image.

The code for my perspective transform includes a function called `transform_perspective()`, which appears in 4th code cell of the IPython notebook ("./AdvancedLaneFindingImage.ipynb").  The `transform_perspective()` function takes as inputs an image (`img`)..  I chose the hardcode the source and destination points in the following manner:

```python
src = np.float32(
    [[(img_size[0] / 2) - 55, img_size[1] / 2 + 100],
    [((img_size[0] / 6) - 10), img_size[1]],
    [(img_size[0] * 5 / 6) + 60, img_size[1]],
    [(img_size[0] / 2 + 55), img_size[1] / 2 + 100]])
dst = np.float32(
    [[(img_size[0] / 4), 0],
    [(img_size[0] / 4), img_size[1]],
    [(img_size[0] * 3 / 4), img_size[1]],
    [(img_size[0] * 3 / 4), 0]])
```

This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 585, 460      | 320, 0        | 
| 203, 720      | 320, 720      |
| 1127, 720     | 960, 720      |
| 695, 460      | 960, 0        |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][image4]

#### 4. Identified lane-line pixels and fit their positions with a polynomial?

The code for my identifing lane-line includes a function called `find_lane_pixels()`, `fit_polynomial()`, which appears in 6th code cell of the IPython notebook ("./AdvancedLaneFindingImage.ipynb").  Then I fit my lane lines with a 2nd order polynomial kinda like this:

![alt text][image5]

#### 5. Calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I did this in `measure_curvature_real()`, which appears in 7th code cell of the IPython notebook ("./AdvancedLaneFindingImage.ipynb").

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented in `warp_back()`, which appears in 8th code cell of the IPython notebook ("./AdvancedLaneFindingImage.ipynb").  Here is an example of my result on a test image:

![alt text][image6]

---

### Pipeline (video)

#### 1.Final video output.
I changed a little bit for video, because we can use prior position to find lanes.  The code is in IPython notebook ("./AdvancedLaneFindingVideo.ipynb").
Here's a [link to my video result](./project_video_out.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

I tested challenge_video.mp4 and harder_challenge_video.mp4, but they are litterally hard to identify lanes.  And it is hard to find where the problem is , because they are streaming. 
