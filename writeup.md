**Vehicle Detection Project**
---
The goals / steps of this project are the following:

* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier
* Optionally, you can also apply a color transform and append binned color features, as well as histograms of color, to your HOG feature vector. 
* Note: for those first two steps don't forget to normalize your features and randomize a selection for training and testing.
* Implement a sliding-window technique and use your trained classifier to search for vehicles in images.
* Run your pipeline on a video stream (start with the test_video.mp4 and later implement on full project_video.mp4) and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
* Estimate a bounding box for vehicles detected.

[//]: # (Image References)
[image1]: ./OutputImages/Car.JPG
[image2]: ./OutputImages/HOG.JPG
[image3]: ./OutputImages/Windows_detected.JPG
[image4]: ./OutputImages/CarHeat.JPG

---
### Histogram of Oriented Gradients (HOG)

I started by reading in all the `vehicle` and `non-vehicle` images.  Here is an example of one of each of the `vehicle` and `non-vehicle` classes:

![alt text][image1]

I then explored different color spaces and different `skimage.hog()` parameters (`orientations`, `pixels_per_cell`, and `cells_per_block`).  I grabbed random images from each of the two classes and displayed them to get a feel for what the `skimage.hog()` output looks like.

Here is an example using the `YCrCb` color space and HOG parameters of `orientations=9`, `pixels_per_cell=(16,16)` and `cells_per_block=(2, 2)`:

![alt text][image2]

After trying differet values many times below parameter values gave me the best accuracy.

| Parameter | Value |
|-----------|---------|
| Color Space | YCrCb |
| HOG orientations | 9 |
| HOG pixels per cell | 16 |
| HOG cells per block | 2 |
| HOG Channel | ALL |
| Spatial binning dimensions | (16,16) |
| Number of histogram bins | 32 |

#### 3. Describe how (and identify where in your code) you trained a classifier using your selected HOG features (and color features if you used them).

I trained a linear SVM using and extracted the features from image using above parameters.
Below are the training results I got-

`64.28 Seconds to extract HOG features...
(17760, 1836)
(17760,)
Using: 9 orientations 16 pixels per cell and 2 cells per block
Feature vector length: 1836
6.55 Seconds to train SVC...
Test Accuracy of SVC =  0.9828
My SVC predicts:  [1. 1. 1. 1. 0. 1. 1. 0. 1. 1.]
For these 10 labels:  [1. 1. 1. 1. 0. 1. 1. 0. 1. 1.]
0.03502 Seconds to predict 10 labels with SVC`



### Sliding Window Search

Initially I used only single window of (96,96) size to search for cars. With only one window cars were not detected in some frames of the video. To overcome this I used four windows of different size. Also i varied the stride of windows(Overlapping) to detect cars more efficiently. Below images show cars detected by windows of different size.

![alt text][image3]

#### Pipeline

I used and SVM classifier to detect cars in images using the parameters given above. I used YCrCb color space for HOG. Along with hog features I used features extracted by spatial binning and histograms of color. After extracting features from the images I split the images into training and test data. Using training data I trained the SVM classifier and got 98.28% accuracy.

After training the classifier I fed the frames of videos to pipeline which will apply sliding window, heat maps and detect the cars.

![alt text][image4]
---

### Video Implementation

Here's a [link to my video result](./project_video.mp4)


#### Removing False Positives and Combining Overlapping Bounding Boxes

I recorded the positions of positive detections in each frame of the video.  From the positive detections I created a heatmap and then thresholded that map to identify vehicle positions.  I then used `scipy.ndimage.measurements.label()` to identify individual blobs in the heatmap.  I then assumed each blob corresponded to a vehicle.  I constructed bounding boxes to cover the area of each blob detected.  
Here's an example result showing the heatmap from a series of frames of video, the result of `scipy.ndimage.measurements.label()` and the bounding boxes then overlaid on the last frame of video:



### Here are six frames and their corresponding heatmaps:

![alt text][image5]

### Here is the output of `scipy.ndimage.measurements.label()` on the integrated heatmap from all six frames:
![alt text][image6]

### Here the resulting bounding boxes are drawn onto the last frame in the series:
![alt text][image7]



---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

Here I'll talk about the approach I took, what techniques I used, what worked and why, where the pipeline might fail and how I might improve it if I were going to pursue this project further.  

