# **Finding Lane Lines on the Road** 
## 0. Overview
When we drive, we use our eyes to decide where to go.  The lines on the road that show us where the lanes are act as our constant reference for where to steer the vehicle.  Naturally, one of the first things we would like to do in developing a self-driving car is to automatically detect lane lines using an algorithm.

In this project I will detect lane lines in images using Python and OpenCV.  OpenCV means "Open-Source Computer Vision", which is a package that has many useful tools for analyzing images.  

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on my work 
## 1. Pipeline
My pipeline consisted of 5 steps. First, I converted the images to grayscale, then a Gaussian blur is applied on the grayscale image to decrease gradient in color to remove unwanted edge detection in later stages. Next, Canny edge detection is used. It's noticed that parameters in Canny algorithm play important roles in detecting edges. To avoid processing the area that doesn't contain lanes, a Region of Interest (ROI) is defined with a polygon. Edges outside of ROI are ignored. Finally, the masked canny image is fed into hough transform and corresponding lines are drawn on our copy of original image.
Note that the output of Hough transformation is multiple short lines, instead of a single continuous line. In order to draw a single line on the left and right lanes, I modified the `draw_lines()` function with the following procedures:
1. Divide the lines from Hough transformation into two groups: `left_lines` and `right_lines`, depending on their slopes. 
2. Put all points in `left_lines` and `right_lines` into two seperate lists of tuples: `[(x1,y1),(x2,y2),...(xn,yn)]`
3. Fit these points with a line, using `numpy.polyfit`
4. Find the top and bottom limit for left and right line respectively. Since both lines start from the bottom of the image, maximum of y (bottom limit, `y_1_l` or `y_1_r`) is `image.shape[0]`. Top limit (`y_2_l` or `y_2_r`) is set as `min(y)`, where `y` is the y coordinates of the points in left_lines or right_lines. 
5. Calculate corresponding x coordinates regards to the y limits found in previous step. `(x1_l,y1_l)` and `(x2_l,y2_l)` are the two points of the consolidated left line. Similarly, `(x1_r,y1_r)` and `(x2_r,y2_r)` are the two points of the consolidated right line.

## 2. Potential shortcomings 
Identification of curved lanes is not capable with current approach, since a straight line is used to extrapolate the points. 
Selection of ROI is not accurate, and relies on trial and error (manually). Parameters used in Canny edge detection and Hough transformation are also picked in similar inefficient manner. 

## 3. Possible improvements
A possible improvement would be to use curve fitting to identify curved lanes.
## 4. Challenge problem
The `challenge.mp4` presents a more tricky case: it has both yellow and white lanes, and the lanes are curved.
A `color_mask` is utilized to accurately find the yellow lanes. The original image is converted into HSV image for the color filter to work. Again, the current approach doesn't draw curved lanes. 


