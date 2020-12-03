# **Finding Lane Lines on the Road** 

### Reflection

[img-edges]: ./examples/tmp-edges.jpg
[img-clipped]: ./examples/tmp-clipped.jpg
[img-line-segments]: ./examples/tmp-line-segments.jpg
[img-lines]: ./examples/tmp-line.jpg

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consisted of 5 steps. First, I converted the images to grayscale, blurred it a bit and ran Canny edge detection algorithm on the blurred image. I've got the following results:

![Detected edges][img-edges]

Then I clipped the region of interest in this image: the trapezoid area in the bottom
center of the image containing the lane the vehicle is moving in:

![Clipped region][img-clipped]

Then I ran Hough transformation in order to detect lines segments in this region:

![Detected line segments][img-line-segments]

Finally, I converted the found segments into 2 lane lines:
1. split the segments into 2 groups: left segments and right segments
2. "average" the segments of each group into a line using `cv2.fitLine()`

The result was as follows:

![Average lane lines][img-lines]

Finally, I blended the resulting image with the original one and returned the
result.

### 2. Identify potential shortcomings with your current pipeline

The main shortcoming is that the lane lines are affected by any detected line in
the region of interest. That is, if there is a patch on the road then its
horizontal lines will detected and taken into account. The result will be that
the detected lines will be off from the real ones by a lot. In fact, you can see
this effect in `./test_videos_output/challenge.mp4` -- the tree shadows on the
road cause the detected lines to be almost horizontal.

Another of the shortcoming comes from the fact that the region of interest is
"hardcoded". If the camera is shifted a bit and the lanes go out of this region
the pipeline will fail and will return a complete "garbage".

### 3. Suggest possible improvements to your pipeline

A possible improvement would be to define some thresholds for lines taken into
account when calculating the average one. For example, if the line horizontal or
almost horizontal it should be ignored.

Another potential improvement could be to ignore the hood of the vehicle at the
bottom of the image. Try to detect it in the image. If it is detected, then the
clipped region of interest should not include it.
