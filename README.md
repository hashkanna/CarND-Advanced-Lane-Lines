## Advanced Lane Finding
[![Udacity - Self-Driving Car NanoDegree](https://s3.amazonaws.com/udacity-sdc/github/shield-carnd.svg)](http://www.udacity.com/drive)

[//]: # (Image References)

[image_0]: ./md_resources/image_0.png "Undistorted Chessboard"
[image_0_1]: ./md_resources/image_0_1.png "Undistorted Sample Image"
[image_1]: ./md_resources/image_1.png "Birds-eye View"
[image_2]: ./md_resources/image_2.png "Bit Mask Extraction"
[image_2_1]: ./md_resources/image_2_1.png "Gaussian Blur"
[image_3]: ./md_resources/image_3.png "Histogram Point Fit"
[image_3_1]: ./md_resources/image_3_1.png "Histogram Point Fit"
[image_4]: ./md_resources/image_4.png "Polynomial Fit"
[image_5]: ./md_resources/image_5.png "Lane Augmented"
[image_6]: ./md_resources/image_6.png "Close Up"



The Project
---

The goals / steps of this project are the following:

##### 1. Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
Camera Calibration is performed to remove distortions that arise out of the way cameras perform when taking photographs. The distortions include wide-angle, fish eye and 2D representations of 3D real world images

The findChessboard corners function from OpenCV identifies the 9x6 corners from the calibration images in the cal_images folder. The calibrateCamera function then performs calibration using the 2D and 3D representations of the calibration images in cal_images folder to calculate the calibration image matrix and distortion coefficients

![alt text][image_0]

##### 2. Apply a distortion correction to raw images.
The calibration image matrix and distortion coefficents are fed into the Undistort function and applied on the 2D images of the 3D world coming from the car.

![alt text][image_0_1]

##### 3. Use color transforms, gradients, etc., to create a thresholded binary image.

Color Pixel extractions were performed using the details in the below table to perform vaious colour bitmasks like finding the yellow line using the yellow mask and white lane lines using a white mask on HSV representations of the RGB images and using the InRange threshold function. 

```image_HSV = cv2.cvtColor(warped,cv2.COLOR_RGB2HSV)

yellow_hsv_low  = np.array([ 0,  100,  100])
yellow_hsv_high = np.array([ 80, 255, 255])
yellow_mask = cv2.inRange(image_HSV,yellow_hsv_low,yellow_hsv_high)
yellow1 = cv2.bitwise_and(warped, warped, mask=yellow_mask)

white_hsv_low  = np.array([ 18,   9,   240])
white_hsv_high = np.array([ 45,  45, 255])
white_mask = cv2.inRange(image_HSV, white_hsv_low, white_hsv_high)
white1 = cv2.bitwise_and(warped, warped, mask=white_mask)
```
 
##### Visualization of Bit Mask

![alt text][image_2]
 
Gaussian Blur is applied on the images.

![alt text][image_2_1]


##### 4. Apply a perspective transform to rectify binary image ("birds-eye view").
Perspective Transform is performed to create a birds-eye view of each image. Four points are identified using the below formula and then the cv2 functions getPerspectiveTransform and warpPerspective are used to perform the perspective transformation on each image from the car to create a corresponding rectangle/birds-eye view image. 

```ht_window = np.uint(img_size[0]/1.5)
hb_window = np.uint(img_size[0])
c_window = np.uint(img_size[1]/2)
ctl_window = c_window - .2*np.uint(img_size[1]/2)
ctr_window = c_window + .2*np.uint(img_size[1]/2)
cbl_window = c_window - .9*np.uint(img_size[1]/2)
cbr_window = c_window + .9*np.uint(img_size[1]/2)
```
![alt text][image_1]

##### 5. Detect lane pixels and fit to find the lane boundary.
Lane lines were found by sliding a histogram window. At each slice a point was indexed where the highest density of pixels were found. 
![alt text][image_3]
![alt text][image_3_1]

##### 6. Determine the curvature of the lane and vehicle position with respect to center.
The radius of curvature is computed by scaling the pixel values of the lane into meters using some scaling factors and these values are then used to compute the polynomial coefficients in meters and then the provided formula is used to compute the radius of curvature.

##### 7. Warp the detected lane boundaries back onto the original image.
Lanes were imposed by taking the polynomial fit points and feeding them int opencv's polyfill function to fill the polygon. The new polygon was then undistorted, truncated, and imposed onto the road.

![alt text][image_5]

##### 8. Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.
The final output is available in [project_video_result.mp4]
