---
layout: page
permalink: /flexible-edof/
mathjax: true
---
## Extended Depth of Field photography

Lens law in optics says that for a lens of a fixed focal length
\\(f\\) and the sensor plane at a distance \\(v\\) from the lens, only
a particular plane (parallel to the sensor plane) can be focused. The
distance of this plane \\(u\\) from the lens is given by

\\begin{align} 
  \\frac{1}{f} = \\frac{1}{u} + \\frac{1}{v}.
\\end{align} 

Hence, in this case, the depth of field is a infinitesimal volume,
that is a plane at a particular disance from the lens.

In a practical camera, the depth of field is determined by the pixel
size. Any object not at \\(v\\) will be blurred at the sensor defined
by a blur circle (also known as circle of confusion). If the radius of
the blur circle spans a distance less than the pixel size, then we
consider the blur to be ignorable and we say that the scene is
focused. Hence, the depth of field is a 3D volume in the scene
(integration of consecutive planes). 

The width of the depth of field volume (i.e. the depth of field value)
is determined by the aperture of the camera. A pinhole camera with
very-close-to-zero aperture has an infinite depth of field; it focuses
objects at all depths in the scene. If the aperture is large enough,
then the depth of field becomes close to zero.

If the scene is brightly lit, then the aperture can be made as low as
possible in a camera to capture an image that focuses all depths. On
the other hand, if the scene is lit at a medium level, we need to
increase the aperture. One could increase the expoure time instead to
collect more photons, but it is limited by the dynamics of the
scene. Increasing the aperture thus produce a limited depth of field
image. If the aperture is reduced, the image will be affected by noise
(due to less number of photons) and thus the SNR falls low. In
reality, we want a range of depths to be at focus (if not, all
depths), and this is referred to as <i>extended</i> depth of
field. The question is how to capture an extended depth of field image
at a higher aperture setting?