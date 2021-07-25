# **Finding Lane Lines on the Road** 

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report

[//]: # (Image References)

[image0]: ./examples/grayscale.jpg "Grayscale"

[image1]: ./test_images_output/solidWhiteCurve.jpg
[image2]: ./test_images_output/solidWhiteRight.jpg
[image3]: ./test_images_output/solidYellowCurve.jpg
[image4]: ./test_images_output/solidYellowCurve2.jpg
[image5]: ./test_images_output/solidYellowLeft.jpg
[image6]: ./test_images_output/whiteCarLaneSwitch.jpg

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consists of the following steps:

1. turn the image into gray

2. apply gaussian smoothing

3. apply canny to detect edges

4. set the region of interest. To calibrate the parameters I drew the defined region on images from the folder test_images

5. combine the detected edges and the region of interest to get the masked edge

6. To decide which lines are left lane lines and which are right, I used the center line of the region of interest.

7. set the parameters of Hough transformation and apply the hough transformation using the helper function hough_line.

8. connect and extrapolate the left and right lane lines. To achieve this, I did the following steps in the helper
function draw_lines: 

    8.1. To assure that this function can act as previously, I added the mode of this function (this helped me a lot while
    debugging and calibration of the parameters of hough transformation):
    - mode = 1: detected lines will NOT be connected or extrapolated.
    - mode = 2: detected lines will be connected and extrapolated.
    
    8.2. For the mode 2 I used the center line of the region of interest to decide which of the lines belongs to left
    lane line and which to right.
    
    8.3. Apply np.polyfit each to the left lines and right lines to get line function of each left and right lane line
    
    8.4. Extend the left and right lane lines to the upper and bottom boundaries of the region of interest.
    
    8.5. draw the lane lines

The following images are the results of the pipeline:
- solidWhiteCurve
![alt text][image1]

- solidWhiteRight
![alt text][image2]

- solidYellowCurve
![alt text][image3]

- solidYellowCurve2
![alt text][image4]

- solidYellowLeft
![alt text][image5]

- whiteCarLaneSwitch
![alt text][image6]

I tried this pipeline to the two test videos. The lane lines could be correctly detected, but as a video the detected left
and right lane lines oscillated while driving. To achieve the stable lane lines like the example videos, I did the following
steps:

1. The idea is to store the images from the previous frames into a global variable and use these to hough transformation,
 so that the detected lane lines depend not only on the present frame but also the previous frames. In this way the transition 
of the position of detected lane lines from different frames should be softened and the detected lane lines should be seen more stable.

2. I wrote the pipeline "lane_line_finding_pipeline_premium" and correspondingly the helper function 'hough_line_premium' to
try this idea.

3. First in the new pipeline, I added a new variable "filter_size" to determine, how many previous frames I take while
drawing the lane lines.

4. Then in the helper function "hough_line_premium" I took the images in memory as a new argument of this function and
 applied hough transformation to each of these images. After that I store all these detected lines into np.array and passed
  this to the funtion "draw_lines". In "draw_lines" all these lines will be taken into account while drawing the lane lines.

After doing these the detected lane lines in the video are more stable. To compare please the videos "solidWhiteRight" vs.
"solidWhiteRight_premium" and  "solidYellowLeft" vs. "solidYellowLeft_premium" in the folder "test_videos_output".

### 2. Identify potential shortcomings with your current pipeline

One shortcoming of my pipeline is to seen in the challenge video: the detected lane lines are totally messed (see video "challenge.mp4"
in folder "test_videos_output"). The reason seemed to be that the camera was mounted to a different position and the car
was in a big curve.


### 3. Suggest possible improvements to your pipeline

A possible improvement would be to using CNN to detect the lane line, so that the position and direction of camera and
the road condition could be handled more robust.
