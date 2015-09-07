---
layout: page
permalink: /flutter-shutter/
mathjax: true
---
## Flutter-shutter camera

**Need for deblurring** 
A brightly-lit scene can be captured by a camera with a low exposure
  setting. The amount of noise observed is negligible. In a low-lit
  scene, keeping the exposure low introduces noise in the image. One
  can increase the aperture to allow for more light at low exposure
  time, but this may not be possible in all situations such as, where
  we might like to focus the full scene, thus requiring the aperture
  to be low. Therefore, increasing the exposure time could be the only
  option. In that case, there is a problem if the scene contains
  moving objects. They cause motion blur due to high exposure. Since
  the scene is not under our control in general, we need to
  post-process the image to remove motion blur after capturing the
  image.

**An example** Let's take a simple example. In the below image, the
  background of the scene is clear and the moving car is blurred. The
  car moves in a linear fashion during exposure. Thus, this is a 1D
  motion blur. We could crop the car and rotate it so that the blur
  becomes horizontal. Our task is to deblur this image. Let us even
  assume that the velocity of the car and the exposure time is
  known. During the exposure time, shifted versions of the car get
  accumulated in the sensor, and the result is the blurred image.

<div class="fig figcenter fighighlight">
  <img src="/ee5176/img/flutter-shutter/blurred.png">
  <div class="figcaption">
   <br>
  </div>
</div>

**Image formation** In mathematical notation, the blurring operation
is written as a convolution.  
$$ y = x \ast h $$ 
Here \\(y\\) is the
blurred image, \\(x\\) is the clean image, and \\(h\\) is the blur
kernel. Since, the exposure of the camera is open at an instant and
closed after a certain time, \\(h\\) is a box filter. Given \\(y\\)
and \\(h\\), our task is to estimate \\(x\\).

Since the operation involved is convolution, we immediately think of
transforming into Fourier domain so that the operation becomes a
simple multiplication.  

$$ Y = X . H $$ 

We can estimate \\(x\\) by \\(F^{-1}(Y/H)\\). This seems perfectly okay
at first glance, but there is a problem. If the Fourier transform of
the kernel is zero at a particular location, then the Fourier
transform of \\(x\\) does not exist at that location. That is, if the
filter blocks any of the frequencies, then those frequencies are lost
forever and cannot be found out exactly.

**Image Priors** 
A oft-used way to deblur is to employ image priors. We impose certain
properties of the clean image which we ought to know beforehand. For
example, one could impose that the image is sparse in wavelet domain,
which is a property of natural images. Thus, one could solve the
following optimization problem:
$$
\min \rho(x) + \\|y - h \ast x\\|^2
$$
The first term is the regularizer or prior, and the second term is the
data term which is based on the image formation model. Some of the
commonly used regularizers are wavelet sparsity, gradient sparsity,
and GMM on gradients.

**Convolving with coded filter**
The blurring operation due to continuous exposure can be viewed as a
convolution of the image with a box filter. If we see the frequency
spectrum of the box filter, it is a sinc function with many zeros at
regular intervals. This hinders the deconvolution process through
Fourier transform as discussed before. Thus, we open and close the
shutter at random intervals during the exposure time so that the
filter is not a box, but a coded one. The frequency response of such a
filter stays put and does not fall to zero at regular
intervals. Thus, it is better for deconvolution.

\\Include DFT of box and coded filters here\\

**Blurring as matrix operation**
The blurring operation can be viewed as a matrix multiplication
between the blurring matrix and the image.
\\begin{align}
y = A x
\\end{align}
The image \\(x\\) is stacked as a single column vector, and so is the
output image \\(y\\). The blurring matrix \\(A\\) has a certain
structure for convolutional blur. In this discourse, we consider a 1D
blur, so that the motion applied on each column is independent. The
matrix formulation in such a case can be written as
\\begin{align}
Y = A X
\\end{align}
where \\(X\\) is the image itself, and \\(A\\) operates on each column
of \\(X\\) to form the corresponding column in \\(Y\\). Each column of
\\(A\\) contains the filter \\(h\\) with padded zeros. It is a tall
and skinny matrix, and thus the system is over-determined.

**Deblurring**
Let us redefine
\\(y\\) and \\(x\\) to represent any column of the deblurred and
blurred images, respectively. Thus, we have
\\begin{align}
y = A x
\end{align}
We solve this equation using least squares method which gives the
solution,
\\begin{align}
x &= A^+x = (A^TA)^{-1}A^Ty
\\end{align}
What condition should be imposed on \\(A\\) to get a well-posed
solution. For that, we consider the presence of noise $n$.
\\begin{align}
y &= A x + n\\\\
A^+ y &= A^+Ax = A^+n\\\\
A^+ y &= x + A^+ n
\\end{align}
Thus, the noise gets operated by \\(A^+\\). The covariance of this
quantity is 
\\begin{align}
cov(A^+n) &= E[(A^+n)(A^+n)^T]\\\\
&= \sigma^2 A^+A^{+T}\\\\
&= \sigma^2 (A^TA)^{-1}
\\end{align}
The desired covariance should have small diagonal values and zero
off-diagonal values. Therefore, singular values of \\(A\\) should not
be small (How?). Thus, DFT of columns of \\(A\\) should not be zero,
which was what we discussed during the design of an optimal code.

**Choice of code**
Let us consider the code length \\(m\\) to be 52. We want the 50% of the
light to come in during the exposure time, hence \\(1\\)s and \\(0\\)s should
be equally distributed in the code. The code should additionally have
$1$s in the first and last bits. Thus, the total number of possible
codes is \\({}^{50}C_{24} = 1.2 \times 10^{14}\\)!. Our desired code should

- maximize the minimum of the DFT 

- minimize the variance of the DFT. 

It is indeed a challenge to search for the optimal code.

\\Include figures of different codes and DFTs\\

**Effect of blur length** 
So far, we have assumed that the blur length $k$ is equal to the code
length $m$. In real scenarios, we do not know the amount of blur that
will occur. How do we choose the code length? Let us see how
deblurring fares if $k \neq m$. 

\\Include deblurring outputs for different ks for m=52\\

**Conclusions**
The advantage of coded exposure lies in low light scenarios where the
exposure time has to be increased (and if there is a limit in
increasing the aperture). By coding the exposure, we make the
deconvolution problem more well-posed. The zero points in the
frequency domain of the kernel are removed, thereby, preserving the
signal frequencies leading to a more desired deblurred image.