---
layout: page
permalink: /coded-aperture/
mathjax: true
---
## Coded Aperture

Images captured using a conventional camera incur defocus blur at all
different depths other than those inside the depth of field. The depth
information is captured as blur, and thus blur acts as a cue to infer
depth. The depths away from the focal plane are "more blurred" and the
depths closer to the focal plane are "less blurred." 

**Need for image prior **
To infer the depth of an image, one could extract a patch around every
pixel, and based on the blur information present in that patch, its
depth can be estimated. The obvious issue here is how to tell what
amount of blur is present in the patch. A basic ambiguity is the
presence of "blur-like" objects in the scene. One cannot tell whether
an object itself is blur-like, or the object is sharp and appears
blurred due to defocus. In practical scenarios, we consider that the
edges of the objects are sharp, and it follows a natural image
statistic (for example, sharp and sparse gradients). 

**PSF scaling**
The amount of blur at a pixel is related to the amount of defcous,
that is how much the light spreads due to blurring. This is given by
the scale of the PSF at that pixel. The shape of the PSF is directly
determined by the shape of the aperture. The size of the PSF depends
on where the object lies corresponding to that pixel. To determine the
depth, we can run through all scales of the PSF, the shape of which is
given by the aperture, and find out which scale deblurs the patch
around that pixel "best". The depth corresponding to this scale is
then the depth of that pixel.

$$
\text{Deconvolution: } \hat{x} = \arg \min_x \|y - x \ast h\|_2^2 
$$

The catch here is obviously how to tell which scale produces the best
deblurred image. One, the non-blind deconvolution operation might produce
ringing artifacts even when the kernel is exactly correct. Two, even when
the artifact issue is not present, for scales closer to the correct
scale, deblurring results are usually pretty good, and hence we cannot
tell exactly the correct depth. And three, there could be multiple
clean images which lead to the same blurred image for the given kernel
due to the zeros present in the frequency domain of the kernel.

To avoid problems during deconvolution, we need to impose priors on
the image. That is, we need to tell the optimizer that the image we
are seeking will follow certail properties. One of the commonly used
image prior is the sparsity on the gradients. 

$$
\text{Deconvolution: } \hat{x} = \arg \min_x \|y - x \ast h \|_2^2 +
\| \nabla \|_{0.8}
$$

This optimization problem can be solved using iterative reweighted
least squares.

**Discrimination between scaled PSFs**
To be able to infer depth, we need to tell clearly the scale of the
PSF that affects a particular patch. As we saw earlier, scales closer
to the correct scale might also result in a good deconvolution leading
to an ambiguity. Thus, the scales of the PSFs should be able to better
discriminate against each other for better depth
estimation. Conventional aperture (i.e.\ round or polygonal) scores
poorly in this case. Intituitively, this is because scaled versions of
these shapes are the same shapes themselves. One good property is that
the aperture should change its shape at different scales. A fully-open
aperture of any shape will not satisfy this property. Hence, we go for
partially opened apertures, especially apertures having a number of
smaller apertures inside them, so that scaling leads to joining of
these smaller apertures leading to a different pattern. These are
called __coded__ apertures.