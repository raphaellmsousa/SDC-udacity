# **Finding Lane Lines on the Road** 
**Self-driving Car Nanodegree**

Author: Raphaell Maciel de Sousa

Data: 05/26/2017

---

### Reflections

### 1. The Proposed Pipeline

Next, It will be described the used process for line detection and how the **draw_lines** function was modified to extrapolate the identified lines.


### 1.1 **draw_lines function**

The biggest challenge of this first project is how to extrapolate lines. For this purpose, it was used the follows steps:

#### 1.1.1 Separate left and right detected lines

In this case, the Hough Transform (HT) can detect straight lines in an image. But, after HT, there are many detected lines and for the proposed method it is necessary to separate left and right ones. 

A way of separate left and right lines is using the inclination "m". So, that can be achieve by doing:

m = (y2 - y1) / (x2 - x1)

So, in case of m>0, that means a right line, otherwise is a left one. After all, it will be 2 groups of detected points. 

#### 1.1.2 Calculate the mean of the points

The idea here is to find a function of a line (y = mx+b) that pass trought the mean of the detected points. So, first it was calculated the mean of the left and right points:

x1_avg = int(np.mean(x1))

y1_avg = int(np.mean(y1)) 

After that, the means points were used to calculate m and b parameters of the average line:

m = (y2_avg-y1_avg)/(x2_avg-x1_avg) 

b = y2_avg - m * x2_avg

#### 1.1.3 Extrapolation of the detected lines

Now, after calculate a function of the line that pass trought the means points of the left and right sides, it was used the boundaries for extrapolation:

cv2.line(img, (x1_l, botton), (x2_l, top), color, thickness) 

Were top and botton parameters are the limits of the region_of_interest.

### 1.2 **Pipeline description**

The first taks was to detect white lines on the road. For this, it was used the proposed methodology covered by the lessons. 

But it was necessary to detect yellow lines too. The methodology of the lessons, with white line detection can achieve yellow lines detection too. But, it was combined two different masks to increase robustness for the challenge videos. For that, first it was used a hsvscale transform:

def hsvscale(img):
    
    return cv2.cvtColor(img, cv2.COLOR_RGB2HSV)    

And than, yellow and white masks have been defined:

#### Mask definition
yellow_hsv_low  = np.array([ 0, 80, 200])

yellow_hsv_high = np.array([ 40, 255, 255])

white_hsv_low  = np.array([  20,   0,   200])

white_hsv_high = np.array([ 255,  80, 255])

#### Threshold the HSV image to get yellow and white colors
mask_white = cv2.inRange(hsv, white_hsv_low, white_hsv_high)

mask_yellow = cv2.inRange(hsv, yellow_hsv_low, yellow_hsv_high)

So, after that it was used proposed methodology of the lessons.

### Summing up

1. hsvscale transform;

2. Yellow and White Masks definition

3. Masks combination

4. Gaussian_blur function

5. Canny edge detection

6. Hough_lines function 

### 2. Shortcomings of the current pipeline


One potential shortcoming would be to detect lines under high brightness condition. Another problem is to draw curved lines.

### 3. Pipeline improvements

* Discart lines with small m values;
* Use yellow and white masks combined;

### 4. Another tests

It was used differents kinds of filters trying to get better results:

def laplacian(img):
    
    return cv2.Laplacian(img,cv2.CV_64F)

def sobel_x(img):
    
    return cv2.Sobel(img,cv2.CV_64F,1,0,ksize=5)

def sobel_y(img):
    
    return cv2.Sobel(img,cv2.CV_64F,0,1,ksize=5)

def bilateral(img):
    
    return cv2.bilateralFilter(img,9,75,75)
    
But, the presented pipeline demonstreted the best results for this work.    
