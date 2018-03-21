# **Finding Lane Lines on the Road** 

## Description

### This is the first project of self-driving cars engineer nanodegree. In this project we will find lane lines on the road. We will test the pipeline in several images and videos with different situations.

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report

---

## Pipeline

In the first part of the pipeline, I imported the necessary libraries and read a test image.

Next I defined Helper methods and some variables, in this section, we have:

```
# Global Slopes Variables
prevAvgLeftSlope = 0
prevAvgRightSlope = 0
```
These are global variables to save previous slopes values. This will be explained in more detail later in the document

Other methods were added:

```
def avg(values):
    """
    This function returns the average value from list of values
    """
    if (len(values) == 0):
        return 0
    
    return sum(values)/len(values)

def get_X(x0, y0, m, y1):
    """
    This function returns the X value from the line equation given:
    
    x0,y0: known point
    m: known slope
    y1: y value of unknown point x1,y1
    
    line equation ==> (y-y0)=m(x-x0) 
    """
    
    return ((y1-y0)/m) + x0
```
Then, the predefined `draw_lines` was renamed to `draw_border_lines` and `draw_lines` code was changed:

First, some variables are defined to use them later:
```
global prevAvgLeftSlope
global prevAvgRightSlope

xLeftValues = []
yLeftValues = []
leftSlopeValues = []

xRightValues = []
yRightValues = []
rightSlopeValues = []
```

`x[Left|Right]Values`, `y[Left|Right]Values`, `[left|right]SlopeValues` stores x,y points and slopes of the border lines gathered

```
for line in lines:
    for x1,y1,x2,y2 in line:
        slope = (y2-y1)/(x2-x1)
        
        if slope < -0.5 and slope > -0.8:
            xLeftValues.append(x1)
            xLeftValues.append(x2)
            yLeftValues.append(y1)
            yLeftValues.append(y2)
            leftSlopeValues.append(slope)
        elif slope > 0.5 and slope < 0.8:
            xRightValues.append(x1)
            xRightValues.append(x2)
            yRightValues.append(y1)
            yRightValues.append(y2)
            rightSlopeValues.append(slope)
```  
In this code left lines and right lines are separeted and \ the points and slopes are saved in the arrays

```
#left line values and draw   
avgLeftX = avg(xLeftValues)
avgLeftY = avg(yLeftValues)

if prevAvgLeftSlope != 0:
    avgLeftSlope = (avg(leftSlopeValues) + prevAvgLeftSlope)/2
else:
    avgLeftSlope = avg(leftSlopeValues)

prevAvgLeftSlope = avgLeftSlope

if avgLeftSlope != 0:
    leftY1 = img.shape[0]
    leftX1 = get_X(avgLeftX, avgLeftY, avgLeftSlope, leftY1)

    if len(yLeftValues) > 0:
        leftY2 = min(yLeftValues)
    else:
        leftY2 = avgLeftY
        
    leftX2 = get_X(avgLeftX, avgLeftY, avgLeftSlope, leftY2)

    cv2.line(img, (int(leftX1) , int(leftY1)), (int(leftX2), int(leftY2)), color, thickness)
```
Now average of points x,y and slope are calculated to define the straight line equation. As `prevAvgLeftSlope` starts with a value of `0`, we asks if it does not have previous values, just use `avgLeftSlope = avg(leftSlopeValues)` and if it has previous values, makes an average of the current slope with and the previous slope `avgLeftSlope = (avg(leftSlopeValues) + prevAvgLeftSlope)/2` Then, we save the current avg slope in the previous slope.

The next step is to define the 4 points `x1,x2,y1,y2` and draw the line in the straight line image.

We do this only if `avgLeftSlope != 0` because we don't want to draw lines with slope of `0`.

we ask also if `len(yLeftValues) > 0` beacuse we cannot calculate `min()` of an empty array

(This process is similar for the right line) 

NOTE: if we want to see the bordered lines we can use `draw_border_lines()` instead of `draw_lines()` in the `hough_lines()` method

## The pipeline for images consists:

1. Get the images.
2. Convert to grayscale images.
3. Define parameter for Gaussian Blur and run it.
```
# Parameters
kernel_size = 7
```
5. Define parameters for Canny and run it.
```
# Parameters
low_threshold = 100
high_threshold = 200
```

6. Define a region of interest.
```
# Parameters
vertices = np.array([[(80,cannyImages[0].shape[0]),(380, 350), (620, 350), (cannyImages[0].shape[1]-80, cannyImages[0].shape[0])]], dtype=np.int32)
```
7. Define parameters for Hough Transformation and run it.
```
#Parameters
rho = 1
theta = np.pi/180
threshold = 10 
min_line_len = 10
max_line_gap = 20
```
8. Define parameters for weighted images and run it.
9. Save images `test_images_output`.
10. Display the images.

## The pipeline for videos consists:
1. Get the image.
2. Convert to grayscale image.
3. Define parameter for Gaussian Blur and run it.
```
# Parameters
kernel_size = 7
```
5. Define parameters for Canny and run it.
```
# Parameters
low_threshold = 70
high_threshold = 210
```

6. Define a region of interest.
```
# Parameters
vertices = np.array([[(80,cannyImages[0].shape[0]),(380, 350), (620, 350), (cannyImages[0].shape[1]-80, cannyImages[0].shape[0])]], dtype=np.int32)
```
7. Define parameters for Hough Transformation and run it.
```
#Parameters
rho = 1 
theta = np.pi/180
threshold = 10 
min_line_len = 5
max_line_gap = 150 
```
8. Define parameters for weighted image and run it.
9. Return weighted image.

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consisted of 5 steps. First, I converted the images to grayscale, then I .... 

In order to draw a single line on the left and right lanes, I modified the draw_lines() function by ...

If you'd like to include images to show how the pipeline works, here is how to include an image: 

![alt text][image1]


### 2. Identify potential shortcomings with your current pipeline


One potential shortcoming would be what would happen when ... 

Another shortcoming could be ...


### 3. Suggest possible improvements to your pipeline

A possible improvement would be to ...

Another potential improvement could be to ...
