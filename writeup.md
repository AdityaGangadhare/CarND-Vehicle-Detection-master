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
[image5]: ./OutputImages/test1.JPG
[image6]: ./OutputImages/test2.JPG
[image7]: ./OutputImages/test5.JPG
[image8]: ./OutputImages/test6.JPG
[image9]: ./OutputImages/window.JPG

---
### Histogram of Oriented Gradients (HOG)

I started by reading in all the `vehicle` and `non-vehicle` images.  Here is an example of one of each of the `vehicle` and `non-vehicle` classes:

![alt text][image1]

I then explored different color spaces and different `skimage.hog()` parameters (`orientations`, `pixels_per_cell`, and `cells_per_block`).  I grabbed random images from each of the two classes and displayed them to get a feel for what the `skimage.hog()` output looks like.

Here is an example using the `YCrCb` color space and HOG parameters of `orientations=7`, `pixels_per_cell=(16,16)` and `cells_per_block=(2, 2)`:

![alt text][image2]

After trying differet values many times below parameter values gave me the best accuracy.

| Parameter | Value |
|-----------|---------|
| Color Space | YCrCb |
| HOG orientations | 7 |
| HOG pixels per cell | 16 |
| HOG cells per block | 2 |
| HOG Channel | ALL |
| Spatial binning dimensions | (16,16) |
| Number of histogram bins | 32 |
| Kernal | rbf |

#### Training Classifier

I trained a linear SVM using and extracted the features from image using above parameters. I used an rbf kernel to train the classifier.
Below are the training results I got-

`75.15 Seconds to extract HOG features...`<br /> 
`(17760, 1620)` <br />
`(17760,)` <br />
`Using: 7 orientations 16 pixels per cell and 2 cells per block` <br />
`Feature vector length: 1836` <br />
`48.23 Seconds to train SVC...`  <br />
`Test Accuracy of SVC =  0.99218`  <br />
`My SVC predicts:  [1. 1. 1. 1. 0. 1. 1. 0. 1. 1.]`<br />
`For these 10 labels:  [1. 1. 1. 1. 0. 1. 1. 0. 1. 1.]`  <br />
`0.07606 Seconds to predict 10 labels with SVC`  <br />
  
### Sliding Window Search

For searching cars in image I used sliding window method. I iterated over probable image area using windows of different size where car may be found and classify them if car is detected. Initially I used only single window of (96x96) size to search for cars. With only one window cars were not detected in some frames of the video. To overcome this I used four windows of different size. Also I varied the stride of windows(Overlapping) to detect cars more efficiently.I used overlappings ranging from 55% to 85% in x and y directions. Below images show cars detected by windows of different size.

Below are the windowns that I used-

`64x64, 96x96, 128x128, 160x160`

![alt text][image9]

#### Pipeline

I used and SVM classifier to detect cars in images using the parameters given above. I used YCrCb color space for HOG. Along with hog features I used features extracted by spatial binning and histograms of color. After extracting features from the images I split the images into training and test data. Using training data I trained the SVM classifier and got 99.21% accuracy. Below are the steps in pipeline-

- Collect Data
- Extract Features
- Normalize Features
- Split Data
- Train Classifier
- Feed the pipeline with frames of video
- Apply Sliding Window
- Creat Heatmap to remove false detections
- Merge with original image

### Output of test images

![alt text][image5]
![alt text][image6]
![alt text][image7]
![alt text][image8]

### Video Implementation

Here's a [link to my video result](./project_video.mp4)


#### Removing False Positives and Combining Overlapping Bounding Boxes

The sliding window search returns number of false positives. I created a heatmap to identify cluster of overlapping bounding boxes which are likly to be a car. Then I applied an threshold to the heatmap to remove the false positive detections. 
Below are example of created heat map and the final combined image.

![alt text][image5]

---

### Possible Limitations

The pipeline above did an fairly good job in detecting cars. It is reatively easy to tune the parameters of the pipeline but its lot more difficult to choose parameters to detect vehicles in varying lighting conditions. Also there were many false detection when I used linear kernal for SVM classifier, this was solved by using `rbf` kernal but time required to train model is more as compared to using linear kernal. Some more tweaking of parameters is required to detect vehicles of different size ,shape and cars which are at some distance.

---

