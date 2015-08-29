---
layout: page
permalink: /flutter-shutter/
mathjax: true
---

**Need for deblurring** A brightly-lit scene can be captured by a camera with a low exposure setting. The amount of noise observed is negligible. In a low-lit scene, keeping the exposure low introduces noise in the image. One can increase the aperture to allow for more light at low exposure time, but this may not be possible in all situations such as, where we might like to focus the full scene, thus requiring the aperture to be low. Therefore, increasing the exposure time could be the only option. In that case, there is a problem if the scene contains moving objects. They cause motion blur due to high exposure. Since the scene is not under our control in general, we need to post-process the image to remove motion blur after capturing the image.

**An example** Let's take a simple example. In the image, the background of the scene is clear and the moving car is blurred. The car moves in a linear fashion during exposure. Thus, this is a 1D motion blur. We could crop the car and rotate it so that the blur becomes horizontal. Our task is to deblur this image. Let us even assume that the velocity of the car and the exposure time is known. During the exposure time, shifted versions of the car get accumulated in the sensor, and the result is the blurred image.

In mathematical notation, the blurring operation is written as a convolution. 
$$
y = x \ast h
$$
Here \\(y\\) is the blurred image, \\(x\\) is the clean image, and \\(h\\) is the blur kernel. Since, the exposure of the camera is open at an instant and closed after a certain time, \\(h\\) is a box filter.