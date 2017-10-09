**Vehicle Detection Project**

The goals / steps of this project are the following:

* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier
* Optionally, you can also apply a color transform and append binned color features, as well as histograms of color, to your HOG feature vector. 
* Note: for those first two steps don't forget to normalize your features and randomize a selection for training and testing.
* Implement a sliding-window technique and use your trained classifier to search for vehicles in images.
* Run your pipeline on a video stream (start with the test_video.mp4 and later implement on full project_video.mp4) and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
* Estimate a bounding box for vehicles detected.

[//]: # (Image References)
[image1]: ./car_notcar_vis.png
[image2]: ./HOG.png
[image3]: ./Allfeatures.png
[image4]: ./test11.png
[image5]: ./test12.png
[image6]: ./test21.png
[image7]: ./test22.png
[video1]: ./project_video_output.mp4

---

###Histogram of Oriented Gradients (HOG)

####1. Explain how (and identify where in your code) you extracted HOG features from the training images.


I started by reading in all the `vehicle` and `non-vehicle` images.  Here is an example of one of each of the `vehicle` and `non-vehicle` classes:

![alt text][image1]

From the length of cars and notcars, 
        Number of Car images: 8791
        Number of NotCar images: 8967
we know that the dataset is somewhat already balanced, so there is no need to adjust the size of it.

I then explored different combinations of color spaces and `skimage.hog()` parameters (`orientations`, `pixels_per_cell`, and `cells_per_block`).  I grabbed random images from each of the two classes and displayed them to get a feel for what the `skimage.hog()` output looks like.

Here is an example using the `YCrCb` color space and HOG parameters of `orientations=9`, `pixels_per_cell=(8, 8)` and `cells_per_block=(2, 2)`:


![alt text][image2]

####2. Explain how you settled on your final choice of HOG parameters.

I started with the combination of parameters from the class. It did work fine. I then tweaked the combinations did some research online. People would like to try all of the combination and select the best based on current training process. Dut to limited time, I just took the below combination:
  - color_space = 'YCrCb' # Can be RGB, HSV, LUV, HLS, YUV, YCrCb
  - orient = 11  # HOG orientations
  - pix_per_cell = 16 # HOG pixels per cell
  - cell_per_block = 2 # HOG cells per block
  - hog_channel = 'ALL' # Can be 0, 1, 2, or "ALL"
  - spatial_size = (16, 16) # Spatial binning dimensions
  - hist_bins = 16    # Number of histogram bins

####3. Describe how (and identify where in your code) you trained a classifier using your selected HOG features (and color features if you used them).

I first trained a linear SVM using all of the features (From Traning SVM part of my code). It did not work very well due to too many false positives.

![alt text][image3]

Then I switched to use HOG features only. It worked pretty good.

###Sliding Window Search

####1. Describe how (and identify where in your code) you implemented a sliding window search.  How did you decide what scales to search and how much to overlap windows?

For my first pipeline, I did search random window positions with a adjustable window size in a certain area (X_start_stop, Y_start_stop), did not work very well.

After switching to the second approach, I decided to use the following parameters after tuninng for a while:
 - ystart = 400
 - ystop = 656
 - scale = 1.5
 - color_space = 'YCrCb' # Can be RGB, HSV, LUV, HLS, YUV, YCrCb

####2. Show some examples of test images to demonstrate how your pipeline is working.  What did you do to optimize the performance of your classifier?

Ultimately I searched on two scales using YCrCb all-channel HOG features plus spatially binned color and histograms of color in the feature vector, which provided a nice result.  Here are some example on the left side images:

![alt text][image4]
![alt text][image6]
---

### Video Implementation

####1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (somewhat wobbly or unstable bounding boxes are ok as long as you are identifying the vehicles most of the time with minimal false positives.)

Here's a [link to my video result](./project_video.mp4)


####2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

I recorded the positions of positive detections in each frame of the video.  From the positive detections I created a heatmap and then thresholded that map to identify vehicle positions.  I then used `scipy.ndimage.measurements.label()` to identify individual blobs in the heatmap.  I then assumed each blob corresponded to a vehicle.  I constructed bounding boxes to cover the area of each blob detected.  

Here's an example result showing the heatmap from a series of frames of video, the result of `scipy.ndimage.measurements.label()` and the bounding boxes then overlaid on the last frame of video:

### Here are some test examples and their corresponding heatmaps:

![alt text][image4]
![alt text][image6]


### Here the resulting bounding boxes are drawn onto images on the right side, compared with input iamges on the left side:
![alt text][image5]
![alt text][image7]



---

###Discussion

####1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

Problems:
1. I spent a lot of time on tuning the parameters and I borrowed a set of parameters from other people used with good result
2. The first pipeline should be working well. I will probably give it more tries when I have more time.

Difficulites:
As we can clearly see, when there is a car shwoing up from the left side in the video, the pipeline detects those cars. But people usually filter that out since they see those cars as false positives. I just keep it.

Limitations:
1. My pipeline does show some false positives still. I will try to do the tracking part with averaging, Kalman filtering or some other approaches.

