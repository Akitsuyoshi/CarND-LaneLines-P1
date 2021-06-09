## Finding Lane Lines on the Road

This is my first project of [Self-Driving Car Engineer nanodegree program](https://www.udacity.com/course/self-driving-car-engineer-nanodegree--nd013) in udacity.

---

## Table of Contents

- [Finding Lane Lines on the Road](#finding-lane-lines-on-the-road)
- [Table of Contents](#table-of-contents)
- [Overview](#overview)
- [Reflection](#reflection)
  - [0 Setting](#0-setting)
  - [1 Pipeline Steps](#1-pipeline-steps)
  - [2 Potential shortcomings with my current pipeline](#2-potential-shortcomings-with-my-current-pipeline)
  - [3 Improvements to pipeline](#3-improvements-to-pipeline)
  - [4 Future Feature](#4-future-feature)
- [Reference](#reference)
- [Author](#author)

---

## Overview

The goals/steps of this project are the following:

- Make a pipeline that finds lane lines on the road, using opencv and python
- Reflect on my work

...

|Original Image         |Desired Image          |
|:---------------------:|:---------------------:|
|![alt text][original]  |![alt text][output]    |

[//]: # (Image References)

[original]: ./examples/solidWhiteCurve.jpg (Original Image)
[output]: ./examples/laneLines_thirdPass.jpg (Output Image)
[outputV]: ./examples/example_output.gif (Output Video)
[image1]: ./examples/grayscale.jpg (Grayscaled Image)
[image2]: ./examples/blur.jpg (Blurred Image)
[image3]: ./examples/edge.jpg (Edged Image)
[image4]: ./examples/masked_edge.jpg (Edged Image)
[image5]: ./examples/line.jpg (Edged Image)

---

## Reflection

### 0 Setting

To set it up to run this script at first, I followed this [starter kit](https://github.com/udacity/CarND-Term1-Starter-Kit) with docker. If you installed docker successfully, you can run jupyter notebook on your local machine by the command below.

```sh
docker run -it --rm --entrypoint "/run.sh" -p 8888:8888 -v `pwd`:/src udacity/carnd-term1-starter-kit
```

### 1 Pipeline Steps

My pipeline consisted of 5 steps.

First, I converted the images to grayscale, and then applied Gaussian blur to that grayscaled image so that I can reduce image noise. Gaussian blur can be skipped because of cv2.Canny I use at the next step does blur internally, by kernel size 5 × 5.
However, I didn't skip it because I found that 3 × 3 case made a bit better result than that of 5 × 5.

To detect edges in a blurred image, I use Canny edge detection. I decide on a high/low threshold, 255 and 255/3 for that Canny but I couldn't find a convincing reason for that range. While seeing test images, I found that lane lines are mostly located in a specific area, so I put a four-sided polygon to mask at the bottom half of the image.

I got a masked edge image from the pre-processing steps above. I implemented a Hough Transform to decide which lines my pipeline should detect in the image. I first found lines seem close to the expected output, but there's a problem. The lines were oftentimes broken in the middle of the lane when it couldn't see the edges at the bottom of the image.

So, to draw a solid single line on the left and right lanes, I extrapolate a line with `numpy.polyfit`. I do that by simply plugging in points that are outside of the data set. Then I put the original and red line image together to get the final output image.

...

To wrap it up here are 5 steps in my pipeline.

1. Convert the original image to grayscale
2. Applied Gaussian blur to the grayscale image
3. Detected edges in the image by Canny edge detection
4. Masked edge image to see an only useful region in the image
5. Drawed line in the image by Hough transform.

...

|1 Grayscaled           |2 Gaussian Blurred     |3 Edge                 |
|:---------------------:|:---------------------:|:---------------------:|
|![alt text][image1]    |![alt text][image2]    |![alt text][image3]    |

|4 Masked Edges         |5 Red Line             |3 Output(Red Line + Original)|
|:---------------------:|:---------------------:|:---------------------:|
|![alt text][image4]    |![alt text][image5]    |![alt text][output]    |

The output in video format looks like this.

![alt text][outputV]

You can see the codebase in [my jupyter notebook](./P1.ipynb).

### 2 Potential shortcomings with my current pipeline

One potential shortcoming would be that my pipeline finds it hard to get edges when it is bad weather like strong snow in the night. Also, I only tested images in the day but the night image might make a bad result because a border can be a bit unseen relatively.

Another shortcoming could be a bad result when riding on the high hill place. My current pipeline doesn't consider the case that lane has some slope.

### 3 Improvements to pipeline

A possible improvement would increase the test dataset to see how bad or good my current pipeline can detect lane lines. Regardless of its result, it might need more data.

Another potential improvement could implement alternative edge detection or Hough transform to detect the lines in the image. In addition to that, pre-process steps have more room to improve. In this project, I manually change the parameters in the preprocess step, but it can improve more, like [this approach](https://medium.com/@maunesh/finding-the-right-parameters-for-your-computer-vision-algorithm-d55643b6f954)

### 4 Future Feature

The current pipeline couldn't work at all for challenging video, which is something with a lot of curves and objects on the road. Not a high priority to pass, but I'll see it later to deal with that.

---

## Reference

- [How to extrapolate lines with numpy.polyfit](https://peteris.rocks/blog/extrapolate-lines-with-numpy-polyfit/)
- [Finding the right parameters for computer vision](https://medium.com/@maunesh/finding-the-right-parameters-for-your-computer-vision-algorithm-d55643b6f954)

---

## Author

- [Tsuyoshi Akiyama](https://github.com/Akitsuyoshi)