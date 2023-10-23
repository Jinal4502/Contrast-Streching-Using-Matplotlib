# Contrast Stretching Using Matplotlib

## What is contrast stretching and why do we need it?

Oftenly, photographs or medical images obtained from CT-Scan, MRI-Scan, etc. require some particular image enhancements for viewing the details hidden in the images which may not be visible through naked eyes.

Thus, here contrast stretching can be proved useful! It is basically an operation applied on images, which improves the contrast and visibility of the image so that even minor details can be examined.

## How does contrast stretching work ?

So, basically it works on the principle of point operation. Now, we will see in detail what exactly is point operation.

Point Operation means directly applying contrast stretching on the image by modifying each pixel rather then doing it with a kernel.

By using kernel, we usually determine each pixel value on the basis of its neighbourhood pixel values. But instead, here we will use a normalization formula to modify the intensity value of each pixel.

<b>In short, we are transforming intensities of pixels to a bigger range.</b>

As, this whole process of contrast stretching is a linear operation, so the value of new pixel is linearly dependent on the value of the original pixel. Also, because the transformation applied is linear, so we can convert the contrast-enhanced image to the original image.

In this process, we map original intensity range to new intensity range, which is determined using the histogram of an image, so that the minimum and maximum intensity values present in the image are accurately selected excluding the outliers.

```
Histograms is a graphical representation that organizes a group of data points into user-specified ranges.
```

## Image Normalization Formula

```
P_out = (P_in - Min_P) * (((Max_P_out - Min_P_out) / (Max_P_in - Min_P_in)) + Min_P_out) 

P_out = Value of Output Pixel
P_in = Value of Input Pixel
Max_P_out = Maximum value of pixel in output image
Min_P_out = Minimum value of pixel in output image
Max_P_in = Maximum value of pixel in input image
Min_P_in = Minimum value of pixel in input image
```

Now, we will see the implementation of contrast stretching using matplotlib:

Here, we will require libraries like cv2, numpy and matplotlib. 'cv2' library for image reading, resizing and converting. 'maptplotlib.pyplot' will be used for displaying histogrms.

```
# import the required library
from cv2 import cv2
import numpy as np
import matplotlib.pyplot as plt
```

Using the cv2.imread() function, we will load the image into variable called img and with the help of cv2.resize function, we will resize the image to our desired dimensions. Using img.shape, we can view the shape i.e. the dimensions of the image.

```
# load the image into variable called img and resize it
img = cv2.imread('flower.jpeg')
img = cv2.resize(img, (600,400))
img.shape
```

### Output:

```
(400, 600, 3)
```

Here, with the use of cv2, we can convert the RGB image to binary image i.e. gray scaled image. Also, the cv2.convertScales is used to adjust the contrast value and the brightness of the image.

```
# cv2 converts the RGB image to binary image. 
gray_img = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
# increasing the contrast value by alpha and brightening the image by beta
gray_img = cv2.convertScaleAbs(gray_img, alpha=1.10, beta=40)
```

Now, we will display the unstretched i.e. the original image which we converted into gray image. You can see that the white pixels are more in quantity than the gray pixels, so we need to balance out the constrast.

```
# unstretched image
cv2.imshow('original image', gray_img)
cv2.waitKey(0)
```

Output:

<img src=''>

Here, I have used an user-defined histogram function, you can also use the in-built histogram function available in the pyplot submodule of python library.

```
# Histogram of original image
def hist(image):
    h = np.zeros(shape=(256,1))
    s = image.shape
    for i in range(s[0]):
        for j in range(s[1]):
            k = image[i,j]
            h[k,0] = h[k,0] + 1
    return h
histg = hist(gray_img)
plt.plot(histg)
plt.show()
```

Now comes the main stretching code. I have used the normalization formula which I mentined before. We will store the new pixel values in variable 'stretch' and then replace it with the pixel values of original gray image.
```
# Stretching Code:
x = histg.reshape(1,256)
y = np.zeros((1,256))
s = gray_img.shape
for i in range(256):
    if x[0,i] == 0:
        y[0,i] = 0
    else:
        y[0,i] = i
        
min_ = np.min(y[np.nonzero(y)])
max_ = np.max(y[np.nonzero(y)])

stretch = np.round(((255-0)/(max_-min_))*(y-min_))
stretch[stretch<0] = 0
stretch[stretch>255] = 255

for i in range(s[0]):
    for j in range(s[1]):
        k = gray_img[i,j]
        gray_img[i,j] = stretch[0,k]
```

Now, we will view the histogram of the stretched image. You can see that now the histograms seemes more densed.

```
# histogram of stretched image
histg2 = hist(gray_img)
plt.plot(histg2)
plt.show()
```

### Output:

<img src = ''>

So, finally here is the stretched image, one can clearly see that the gray and white pixels are more evenly distributed than before and significantly the contrast of the image has improved.

```
# stretched image
cv2.imshow('Stretched Image: ', gray_img)
cv2.waitKey(0)
```

### Output:

<img src=''>

For more reference refer:

https://pythontic.com/image-processing/pillow/contrast%20stretching
https://www.youtube.com/watch?v=pb5qEoEGy6E

Image Credit:

http://www.aforgenet.com/