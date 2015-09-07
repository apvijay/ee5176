---
layout: page
permalink: /motion-invariant/
mathjax: true
---
## Motion-invariant photography

**Motivation**
Scenes with moving objects are difficult to image especially with high
exposure due to motion blur. One option is to segment the image into
different regions based on moving objects and deblur each of them
individually. But this method may lead to artifacts in the edges
between objects (if at all, segmentation is perfect). How to do away
with the segmentation and space-variant deblurring?

**Track objects**
If the blur is space-invariant, then the deblurring will output better
images that that of space-variant. If we keep the camera static in a
dynamic scene, the blur will always be space-variant (assuming
different objects move with different velocities). The important
observation to make here is that the background will appear clean in
this case, since its velocity is zero. In other words, the camera is
tracking the background (since both their velocities are equal, yes,
zero). If we move the camera with a velocity \\(v\\) in a dynamic
scene, then objects moving with velocity \\(v\\) will appear
clean. Thus, if we could track all objects within a single exposure
time, it would be imaged clean at some point in the exposure. But,
what is its use?

**Make it space-invariant**
Consider you are looking at a road with many vehicles. The road will
look clean to your eyes, and the fast moving cars will look
blurred. Now, shake your head fast, left and right continuously. Now,
the road also will look blurred in addition to cars. But, the blur is
same for all objects and the road. That is, if you want to deblur
easily, blur more. Counter-intuitive?!

**Sweeping the camera**
If we know the range of possible velocities of objects in the scene,
say \\([-v_max,v_max\\]), then we can sweep the camera through all
these velocities during a single exposure time. This would create a
space-invarnt blurred image

//explain more//
