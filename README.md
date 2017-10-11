# **Finding Lane Lines on the Road** 

[image1]: ./test_images_output/solidWhiteCurve_1.jpg "Grayscaling"
[image2]: ./test_images_output/solidWhiteCurve_2.jpg "Gaussian smoothing"
[image3]: ./test_images_output/solidWhiteCurve_3.jpg "Canny Edge Detection"
[image4]: ./test_images_output/solidWhiteCurve_4.jpg "Region of interest (ROI) selection"
[image5]: ./test_images_output/solidWhiteCurve_5.jpg "Hough Transform line detection"
[image6]: ./test_images_output/solidWhiteCurve_6.jpg "Bitmap merging"
[image7]: ./test_images_output/challenge.jpg "Frame from challenge.mp4"

### The pipeline

The processing pipeline consists of these steps:

1. Grayscaling:
   ![alt text][image1]
2. Gaussian smoothing:
   ![alt text][image2]
3. Canny Edge Detection:
   ![alt text][image3]
4. Region of interest (ROI) selection:
   ![alt text][image4]
5. Hough Transform line detection:
   ![alt text][image5]
6. Bitmap merging:
   ![alt text][image6]

In order to draw a single line on the left and right lanes, I modified the draw_lines() function:
* Each Hough line is checked, if its slope value is in a certain range to be a left lane line or a right lane line. Nearly horizontal or vertical lines are discarded.
* To avoid spurious false-positives, left lane lines, which actually appear on the right-hand half of the bitmap, and vice-versa are also discarded.
* Slope and intercept values of left and right lane lines are summed up weighted by their line length. This gives long lines a higher weight than smaller lines, because slope and intercept values for longer lines are less error-prone.
* At the end an average value for slope and intercept for both left and right lane lines is calculated and both lines are drawn in a bitmap.

### Shortcomings and improvements

1. The pipeline looks on different grayscale levels only. This is not sufficient, as can be seen in the challenge video: the left yellow line on the bridge has almost same grayscale level than the bridge itself. Therefore the line cannot be detected at all in some frames. A possible improvement would be to use color selection instead of simple grayscaling. The algorithm should look on white and yellow color only, and this should be done independantly from brightness.
2. ROI cropping now has a fixed region. I believe this is not sufficient. Lane lines could occur everywhere in the picture, at least in bottom part. Assuming a fixed region will lead to missed lines when the car moves between different lanes. Maybe one could improve this with a sliding ROI depending on the last detected lane lines. Or one could omit this step completely, if it would be able to classify all the  detected lines correctly (for example by machine learning).
3. The parameters for Hough lines detection are also fixed at the moment. It's not easy to find a satisfying parameter set, which works under all possible conditions. It could be necessary to tweak more or even calibrate these parameters on-the-fly.
4. The algorithm which calculates the average lines is actually not aware of formerly calculated lines. With this information, it would be possible to apply a filter to slope and intercept values. This would result in a smoother positioning of the lane lines.
5. It is also assumed, that there are only two lane lines to be detected: a left and a right one. If the car moves and there are more than two lines in ROI or if there are line splits or mergers, the current algorithm will fail. A possible improvement could be to collect all Hough lines which belong together into one bin. This could be done on looking on slope and intercept values. After that you will have several bins which can be averaged to several lines. And then with some magice fairy dust you could pick the correct left and right line...

### Final note

How difficult it is for the current algorithm to find the lane lines in the challenge video can be seen [here](test_videos_output/challenge_hough.mp4). This video has been generated with an enhanced version of the draw_lines() function which draws all the Hough lines in different colors. Yellow lines are used to calculate the left lane line and green lines are used for the right lane line. Blue lines are discarded because they have either not the correct slope value or they appear on the wrong side of the bitmap.
![alt text][image7]
