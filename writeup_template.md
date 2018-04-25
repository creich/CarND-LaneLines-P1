# **Finding Lane Lines on the Road**

## Writeup Template

### You can use this file as a template for your writeup if you want to submit it as a markdown file. But feel free to use some other method and submit a pdf if you prefer.

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[gray_canny]: ./test_images_output/gray_canny.png "Grayscale Canny"
[hough_image_1]: ./test_images_output/hough_image_1.png "first hough image"
[hough_image_2]: ./test_images_output/hough_image_2.png "second hough image"
[processed_image]: ./test_images_output/processed_image.png "processed image"

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consisted of 4 steps.

1. convert the images to grayscale
2. use the Canny Edge Detection in two different settings to 'filter out' high brightness gradients

  I didn't apply any extra smoothing here, since i was not able to get better results with that. (maybe i did something wrong?).
3. use Hough algorithm to detect lines within the result images of the former Canny transformations

  Here i also used two different parameter sets. The idea was to go for two minor different setups to average out the biggest shortcomings of each of those settings.
  To merge the results of the two different setups, i didn't use the given helper method for Hough. Instead i went for the opencv HoughLinesP() method and collected the list of lines in the first place. then i added a method called filter_lines() which basically consists of 4 sub steps.
  1. select lines with a specific slope -0.8 < s < -0.5 and 0.5 < s < 0.8
  2. filter lines by number of similar (at least 3) slopes (deviation of +- 0.025)
  3. recalculate lines to fit 'region of interest'
  4. filter lines by number of lines with similar start and end points

  From there, i calculate one single line per side using the arithmetic mean of the filtered lines.

4. just render lines over starting image

![alt text][gray_canny]
![alt text][hough_image_1]
![alt text][hough_image_2]
![alt text][processed_image]


### 2. Identify potential shortcomings with your current pipeline

1. only straight lines would be detected
2. still very sensitive. so most probably wont work on different weather conditions
3. some frames don't detect lines, so there is a flickering lane marker

### 3. Suggest possible improvements to your pipeline

- use common sence to avoid flickering: once there was a line in former frames, it is very unlikely that this line will suddenly disappear! so if we dont't find anything in only one frame, maybe stick to the old shown lane (at least for the following 2 frames or something like that)
- once we know about lines from former frames, maybe use that info to flatten the movement of the lane markers. so average out the position of those markers
- don't just rely on gradient based line detection
