---
title: "DIP_base_09"
date: 2022-02-25T09:19:34+08:00
lastmod:
tags: []
categories: []
slug:
draft: true
typora-root-url: ..\..\..\..\localImages
---


# Image Processing:
## Introduction & Fundamentals

General Information
The purposes of this course:
-	Understand the basic concepts, principles, and methods of DIP
-	To solve the problems in DIP 
-	To deep research the methodology of DIP and some relative subjects, such as:
-	Computer Vision 
-	Graphics
-	Image Retrieval 
-	Biometric
-	…
General Information
Prerequisites:
-	Knowledge of at least two of the following: linear algebra, probability theory, signals and systems


Test & Project:
-	Final exam (70%) 
-	2 assignments (10%+20%)

Contents of this course
-	Introduction & Fundamentals
-	Image Enhancement（时间多一点） 
-	Morphological Image Processing
-	Image Segmentation 
-	Image Restoration
-	Color image processing
-	Representation & Description
-	Object Recognition
-	Image Compression
Contents of Introduction & Fundamentals
This lecture will cover:
-	What is digital image processing?
-	Human vision system
-	Image acquisition
-	Sampling and Quantization
-	Resolution
-	Basic Relationships Between Pixels
-	Key stages in digital image processing

**What is a Image?**
An image may be defined as a two-dimensional function, f(x,y), where x and y are spatial coordinates, and the amplitude of f at any pair of coordinates (x,y) is called the intensity强度 or gray level灰度级 of the image at that point.

- The description of a **digital image**, when *x, y* and the amplitude values of *f* are:
    - Finite值域有限 
    - discrete quantities离散
- Each of element having a particular location and values, these elements are called: Pixels像素

What is a Digital Image?
- Another definition: digital image is referring to the image which is stored in digital format.
- Most of the photos, advertisement, poster that we see in living are simulant images.
- We can digitize them by using scanner, digital camera, or digital video recorder.

What is a Digital Image?

![image-20220605143045071](/image-20220605143045071.png)




Pixel values typically represent gray levels, colors, opacities, etc
Remember digitization implies that a digital image is an approximation of a real scene

What is a Digital Image? 
Common image formats include:

-	1 sample per point (B&W or Grayscale)
-	3 samples per point (Red, Green, and Blue)
-	4 samples per point (Red, Green, Blue, and “Alpha”, a.k.a. Opacity)
For most of this course we will focus on grey-scale images.

What is Digital Image Processing?
Digital image processing:
-	processing digital images by means of a digital computer
Digital image processing focuses on two major tasks
-	Improvement of image quality for human interpretation
-	Processing of image data for storage, transmission, display and representation for automatic machine perception

What is DIP? (cont…)
-	Some argument about where image processing ends and fields such as image analysis and computer vision start.
-	The continuum from image processing to computer vision can be broken up into low-, mid- and high-level processes.

![image-20220605143433906](/image-20220605143433906.png)

What is DIP? (cont…)
Remarks:

- DIP: human is the final explainer.

- Computer vision: computer is the final explainer.

- Computer vision system needs the support of DIP.

- DIP to computer vision is a process from low level to high level processing.

  

Human Visual System

- The best vision model we have!

- Knowledge of how images form in the eye can help us with processing digital images.

-	We will take just a brief tour of the human visual system.

Structure of the Human Eye
Structure of the Human Eye
Blind-Spot Experiment
Draw an image similar to that below on a piece of paper (the dot and cross are about 15cm apart)

Close your right eye and focus on the cross with your left eye
Hold the image about 50cm away from your face and move it slowly towards you
The dot should disappear!
Light and the Electromagnetic Spectrum
Light and the Electromagnetic Spectrum
Light and the Electromagnetic Spectrum

-	Visible light: 0.43~0.79um
-	The electromagnetic spectrum is split up according to the wavelengths of different forms of energy


Where c is the speed of the light, v is the frequency, and h is the Planck’s constant

c=2.998*108 m/s
h=6.626*10-34 J.s or h=4.136*10-15 eV.s 	

Light and the Electromagnetic Spectrum

Light and the Electromagnetic Spectrum

-	The colours that we perceive are determined by the nature of the light reflected from an object
-	For example, if white 
light is shone onto a 
green object most 
wavelengths are 
absorbed, while green 
light is reflected from 
the object
Light reflectance properties	
A body that reflects light and is relatively balanced in all visible wavelengths 
-	Appears white to the observer
A body that favors reflectance in a limited range of the visible spectrum 
-	Exhibits some shades of color
Achromatic or monochromatic light: 
-	The only attribute is intensity--Gray-level
-	Black to Gray to White
Chromatic light 
Three basic quantities to describe the quality of a chromatic彩色 light source:
Radiance (Watts: W)（辐射或发光强度）
-	The total amount of energy that flows from the light source.
Luminance (lumen: lm)(光通量）
-	A  measure of the amount of energy an observer perceives from a light source.
-	Example: Far Infrared Region.
Brightness
-	Subjective descriptor of light perception that is practically impossible to measure.
Light and the Electromagnetic Spectrum
Other EM Spectrum:
 Short-wavelength End
Gamma rays
-	Medical Imaging
-	Astronomical Imaging
Hard X Rays
-	Industrial Applications
Soft X Rays
-	Chest X-Ray (shorter  wavelength end)
-	Dental X-Ray (lower energy end)
Ultraviolet
-	Microscopy Imaging
Visible spectrum
-	Too many applications around us
Infrared region:
-	Near-infrared
-	Far-Infrared
Microwave
-	Microwave Ovens, Communication, Radar
Radio wave
-	AM, FM, TV, and Medical imaging




Visible Imaging



Image Acquisition
Imaging Sensors
Single Sensor Imaging
Sensor Strip Imaging
Sensor Array Imaging
Image Sampling and Quantization
-	Incoming energy lands on a sensor material responsive to that type of energy and this generates a continuous voltage.
Image Sampling and Quantization
Image Sampling and Quantization
Representation
Representation
Representation
Representation
Spatial & Intensity Level Resolution
-	The spatial resolution of an image is determined by how sampling was carried out.
-	Spatial resolution simply refers to the smallest discernable detail in an image.
-	Image size: e.g. 640*480
-	dpi: dots per inch, Newspaper 75dpi, Magazine 133dpi, Poster 175dpi
Spatial & Intensity Level Resolution
Spatial & Intensity Level Resolution
Intensity level resolution refers to the number of intensity levels used to represent the image
-	The more intensity levels used, the finer the level of detail discernable in an image
-	Intensity level resolution is usually given in terms of the number of bits used to store each intensity level
Spatial & Intensity Level Resolution
Spatial & Intensity Level Resolution
Spatial & Intensity Level Resolution
Spatial & Intensity Level Resolution
Spatial resolution: M*N
Gray level resolution：L

How many samples and gray levels are required for a good approximation?
-	Resolution (the degree of discernible detail) of an image depends on sample number and gray level number.
-	i.e. the more these parameters are increased, the closer the digitized array approximates the original image.
-	But: storage & processing requirements increase rapidly as a function of N, M, and k
Spatial & Intensity Level Resolution
The big question with resolution is always: “how much is enough?”
-	This all depends on what is in the image and what you would like to do with it
-	Key questions include
-	Does the image look aesthetically pleasing?
-	Can you see what you need to see within the image?
Spatial & Intensity Level Resolution
The picture on the right is fine for counting the number of cars, but not for reading the number plate

Zooming and Shrinking Digital Images
Zooming: 
-	The creation of new pixel locations
-	The assignment of gray levels to those new locations
-	Nearest neighbor interpolation (NN)
-	Pixel replication: a special case of NN
-	NN produces checkerboard effect
Zooming and Shrinking Digital Images
Zooming: 
-	Bilinear interpolation
-	Using the four NNs of a point. 
-	g(A), g(B), g(C), g(D) are the 
gray levels of pint A, B, C, D.

Zooming and Shrinking Digital Images
Shrinking: Similar manner as just described for zooming.
-	Delete
-	Expand the grid: 
-	Nearest Neighbor interpolation
-	Bilinear interpolation
Basic Relationships Between Pixels
A pixel p at (x,y) has 2 horizontal and 2 vertical neighbors:
-	(x+1,y), (x-1,y), (x,y+1), (x,y-1)
-	This set of pixels is called the 4-neighbors of p: N4(p)

Basic Relationships Between Pixels
Connectivity
Connectivity(连通性) between pixels is important:

-	Because it is used in establishing boundaries of objects and components of regions in an image
Adjacency
We consider three types of adjacency:

-	4-adjacency: two pixels p and q with values from V are 4-adjacent if q is in the set N4(p)

-	8-adjacency : two pixels p and q with values from V are 8-adjacent if q is in the set N8(p)

-	m-adjacency: p & q with values from V are    m-adjacent if
-	q is in N4(p) or
-	q is in ND(p) and the set N4(p)N4(q) has no pixels with values from V
Adjacency
Mixed adjacency is a modification of 8-adjacency and is used to eliminate the multiple path connections that often arise when 8-adjacency is used.
Path
Path
Connected Set and Region
Distance Measures
For pixels p,q,z with coordinates (x,y), (s,t), (u,v), D is a distance function or metric if:

-	D(p,q) ≥ 0 	(D(p,q)=0 if p=q)
-	D(p,q) = D(q,p)  and
-	D(p,z) ≤ D(p,q) + D(q,z)
Distance Measures
Euclidean distance(p(x,y), q(s,t)):

-	De(p,q) = [(x-s)2 + (y-t)2]1/2

-	Points (pixels) having a distance less than or equal to r from (x,y) are contained in a disk of radius r centered at (x,y).
Distance Measures
D4 distance (city-block distance):

-	D4(p,q) = |x-s| + |y-t|
-	forms a diamond centered at (x,y)
-	e.g. pixels with D4≤2 from p

Distance Measures
D8 distance (chessboard distance):

-	D8(p,q) = max(|x-s|,|y-t|)
-	Forms a square centered at p
-	e.g. pixels with D8≤2 from p
Common Distance Definitions
Key Stages in Digital Image Processing
Key Stages in Digital Image Processing:
Image Acquisition
Key Stages in Digital Image Processing:
Image Enhancement
Key Stages in Digital Image Processing:
Image Restoration
Key Stages in Digital Image Processing:
Morphological Processing
Key Stages in Digital Image Processing:
Segmentation
Key Stages in Digital Image Processing:
Object Recognition
Key Stages in Digital Image Processing:
Representation & Description
Key Stages in Digital Image Processing:
Image Compression
Key Stages in Digital Image Processing:
Colour Image Processing
Summary
We have looked at:
-	What is digital image processing?
-	Human vision system
-	Image acquisition
-	Sampling and Quantization
-	Resolution
-	Basic Relationships Between Pixels
-	Key stages in digital image processing

## Image Enhancement:(Point Processing)
What Is Image Enhancement?
Image enhancement is the process of making images more useful
The reasons for doing this include:
-	Highlighting interesting detail in images
-	Removing noise from images
-	Making images more visually appealing
Image Enhancement Examples
Image Enhancement Examples (cont…)
Image Enhancement Examples (cont…)
A Note About Grey Levels
So far when we have spoken about image grey level values we have said they are in the range [0, 255]
-	Where 0 is black and 255 is white
For many of the image processing operations in this lecture grey levels are assumed to  be given in the range [0.0, 1.0]
Spatial & Frequency Domains
There are two broad categories of image enhancement techniques
-	Spatial domain techniques
-	Direct manipulation操作 of image pixels
-	Point processing
-	Neighbourhood operations
-	Frequency domain techniques
-	Manipulation of Fourier transform傅里叶变换 or wavelet transform小波变换 of an image
For the moment we will concentrate on techniques that operate in the spatial domain
Basic Spatial Domain Image Enhancement
Most spatial domain enhancement operations can be reduced to the form
g (x, y) = T[ f (x, y)]
where f (x, y) is the 
input image, g (x, y) is 
the processed image 
and T is some 
operator defined over 
some neighbourhood 
of (x, y)
Basic Spatial Domain Image Enhancement
The simplest spatial domain operations occur when the neighbourhood is simply the pixel itself: Point Processing.
In this case T is referred to as a grey level transformation function or a point processing operation.
Point processing operations take the form:
s = T ( r )
where s refers to the processed image pixel value and r refers to the original image pixel value.
Basic Spatial Domain Image Enhancement
- Point processing
-	The neighborhood is of size 1×1
-	Gray-level transformation
- Mask processing or filtering
-	The neighborhood is defined as a mask, filter, or window.
-	Filtering

Point Processing
In this lecture we will look at image enhancement point processing techniques:
-	Thresholding
-	Logarithmic transformation
-	Power law transforms
-	Grey level slicing
-	Bit plane slicing
-	Image subtraction
-	Image averaging
Thresholding 
Thresholding
Logarithmic Transformations
The general form of the log transformation is 
s = c * log(1 + r)
Log functions are particularly useful when the input grey level values may have an extremely large range of values.

In Fourier transform, we usually encounter spectrum values that ranges from 0 to 106 or higher. But image display system cannot reproduce such a wide range of intensity values.


Logarithmic Transformations (cont…)
In the following example the Fourier transform of an image is put through a log transform to reveal more detail.

Power Law Transformations幂律转化
Power law transformations have the following form:
s = c * r γ      r ∈ [0.0, 1.0]
Map a narrow range 
of dark input values 
into a wider range of 
output values or vice 
versa.
Varying γ gives a whole 
family of curves.
Power Law Example (cont…)
-	The images to the right show a magnetic resonance (MR) image of a fractured human spine. 
-	Different curves highlight different detail.
Power Law Transformations (cont…)
-	An aerial photo of a runway is shown.

-	This time power law transforms are used to darken the image.
Gamma Correction灰度矫正
Many of you might be familiar with gamma correction of computer monitors.
Problem is that display devices, print devices do not respond linearly to different intensities, and they respond according to a power law:

For CRT display, γ =1.8~2.5.
Can be corrected using a nth root transform:
Gamma Correction
Gamma Correction
Gamma Correction
-	Prior Knowledge about gamma correction
-	Varying the value of gamma correction changes not only the brightness, but also the ratio of red to green to blue.
-	Applications
-	Internet
-	Millions of people and millions of monitors
-	Gamma represents an “average ”of the types of monitors and computer systems
-	Scanners, Printers have different values of gamma
Gray Level Slicing
Highlights a specific range of grey levels
-	Similar to thresholding
-	Other levels can be suppressed or maintained
-	Useful for highlighting features in an image
Gray Level Slicing
Gray Level Slicing
Bit Plane Slicing
Often by isolating particular bits of the pixel values in an image we can highlight interesting aspects of that image:
-	Higher-order bits usually contain most of the significant visual information.
-	Lower-order bits contain
subtle details.
Bit Plane Slicing (cont…)
Image Subtraction
Image Subtraction

Image Subtraction
Image Subtraction
Examples: Change Detection
Image Averaging
A noisy image:
Image Averaging
As M increases, the variability of the pixel values at each location decreases.

-	This means that g(x,y) approaches f(x,y) as the number of noisy images used in the averaging process increases.



Image Averaging
Image Histograms图像直方图
The histogram of a digital image with gray levels from 0 to L-1 is a discrete function:




-	rk is the kth gray level
-	nk is the pixels in the image with that gray level
-	k = 0, 1, 2, …, L-1
Normalized histogram: p(rk)=nk/n
-	n is the total number of pixels in the image
-	sum of all components is 1



Image Histograms
Image Histograms
Histogram Examples
A selection of images and 
their histograms.
Notice the relationships 
between the images and 
their histograms.
Note that the high contrast 
image has the most 
evenly spaced histogram.
Histogram Equalization
Histogram equalization
-	Basic idea: find a map T(r) such that the histogram of the modified (equalized) image is flat (uniform).








Histogram Equalization
Steps:
-	Find probability of the input image.
-	Find the cumulative distribution based on the probability  and round the results to obtain sk.



-	Map the original gay-level value to the resulting value obtained in Step 3.








Equalisation Transformation Function
Equalisation Transformation Functions
Histogram Equalization
Summary
We have looked at:
-	Different kinds of point processing image enhancement
-	Histogram processing

## Image Enhancement :(Spatial Filtering空间滤波)
Contents
In this lecture we will look at spatial filtering techniques:
-	Basic of spatial filtering
-	Smoothing spatial filters
-	Smoothing linear filters
-	Order-statistics filters
-	Sharpening spatial filters
-	1st derivative filters
-	2nd derivative filters
Basic of spatial filtering
-	Neighbourhood operations simply operate on a larger neighbourhood of pixels than point operations.
-	Neighbourhoods are 
mostly a rectangle 
around a central pixel.
-	Any size rectangle 
and any shape filter 
are possible.
Basic of spatial filtering
Use of spatial masks for image processing (spatial filters)

-	Correlation
-	Convolution

Basic of spatial filtering
Basic of spatial filtering
Basic of spatial filtering
Basic of spatial filtering
Basic of spatial filtering
Basic of spatial filtering
Strange Things Happen At The Edges!
Strange Things Happen At The Edges! (cont…)
There are a few approaches to dealing with missing edge pixels:
-	Omit missing pixels
-	Can add extra code and slow down processing
-	Pad the image 
-	Typically with either all white or all black pixels
-	Replicate border pixels
-	Truncate the image

Spatial smoothing filtering
Used for noise reduction and blurring (removal of small details prior to large object extraction, bridging small gaps in lines).

-	Smoothing linear filters
-	Order-statistics filters
Smoothing linear Filters
Also called averaging filter
-	Simply average all of the pixels in a neighbourhood around a central value
-	Especially useful 
in removing noise 
from images
-	Also useful for 
highlighting gross 
features
Smoothing linear Filters

Smoothing linear Filters
More effective smoothing filters can be generated by allowing different pixels in the neighbourhood different weights in the averaging function:
-	Pixels closer to the 
central pixel are more 
important
-	Often referred to as a 
weighted averaging
Smoothing linear Filters
-	This process results in an image with reduced sharp transitions in gray levels.
-	Because random noise typically consists of sharp transitions in gray levels, the most obvious application of smoothing is noise reduction.
-	Averaging filters have the undesirable side effect that they blur edges.
-	A major use of averaging filters is in the reduction of “irrelevant” detail in an image.
Smoothing linear Filters
The image at the top left 
is an original image of 
size 500*500 pixels.
The subsequent images 
show the image after 
filtering with an averaging 
filter of increasing sizes:
-	3, 5, 9, 15 and 35.
Notice how detail begins 
to disappear.
Smoothing linear Filters
Smoothing linear Filters
By smoothing the original image we get rid of lots of the finer detail which leaves only the gross features for thresholding.
Order-statistics filters 
Order statistics filters are nonlinear spatial filters whose response is based on ordering the pixels in the area of filter.
Some typical operations include:
-	Min: Set the pixel value to the minimum in the neighbourhood
-	Max: Set the pixel value to the maximum in the neighbourhood
-	Median: The median value of a set of numbers is the midpoint value in that set (e.g. for a 3*3 neighbourhood, with grey level (10,20,20,20,15,20,20,25,100), then we can order them in (10,15,20,20,20,20,20,25,100),20 is the median. Sometimes the median works better than the average
Order-statistics filters 

Order-statistics filters 
-	Filtering is often used to remove noise from images.
-	Sometimes a median filter works better than an averaging filter. 
-	Median filter are particularly effective in the presence of impulse noise, also called salt-and-pepper noise.
Max and Min Filter
Max Filter:


Min Filter:


Max filter is good for pepper noise and min is good for salt noise.
Noise Removal Examples (cont…)
Noise Removal Examples (cont…)
-	For max filter, it will remove pepper noise, but it also removed some dark pixels from the borders of dark objects.
-	The same as min filter, it will remove some white points around the border of light objects.


Adaptive Filters
The filters discussed so far are applied to an entire image without any regard for how image characteristics vary from one point to another.
The behaviour of adaptive filters changes depending on the characteristics of the image inside the filter region.

An increase in filter complexity.
We will take a look at the adaptive median filter.
Adaptive Median Filtering
-	The median filter performs relatively well on impulse noise as long as the spatial density of the impulse noise is not large (Pa and Pb are less than 0.2).
-	The adaptive median filter can handle much more spatially dense impulse noise, and also performs some smoothing for non-impulse noise.
-	An additional benefit of the adaptive median filter is that it seeks to preserve detail while smoothing non-impulse noise.
-	The key insight in the adaptive median filter is that the filter size changes depending on the characteristics of the image.
Adaptive Median Filtering (cont…)
Remember that filtering looks at each original pixel image in turn and generates a new filtered pixel.
First examine the following notation:
-	zmin 	= minimum grey level in Sxy
-	zmax 	= maximum grey level in Sxy
-	zmed 	= median of grey levels in Sxy
-	zxy 	= grey level at coordinates (x, y)
-	Smax 	= maximum allowed size of Sxy
Adaptive Median Filtering (cont…)
The key to understanding the algorithm is to remember that the adaptive median filter has three purposes:
-	Remove impulse noise
-	Provide smoothing of other noise
-	Reduce distortion, by not changing these intermediate-level points. (excessive thinning or thickening of object boundaries) 
Adaptive Median Filtering (cont…)
Level A:	A1 = zmed - zmin
	A2 = zmed - zmax
	If A1 > 0 and A2 < 0, Go to level B
	Else increase the window size
	If window size ≤ Smax repeat level A
	Else output zmed
Level B:	B1 = zxy - zmin
	B2 = zxy - zmax
	If B1 > 0 and B2 < 0, output zxy
	Else output zmed
Adaptive Filtering Example
Sharpening Spatial Filters
Previously we have looked at smoothing filters which remove fine detail.
Sharpening spatial filters seek to highlight fine detail:
-	Remove blurring from images
-	Highlight edges
Averaging is analogous to integration and causes blurring, so differentiation is expected to have opposite results and sharpen an image.
-	smoothing ~ integration
-	sharpening ~ differentiation

1st Derivative
The formula for the 1st derivative of a function is as follows:


It’s just the difference between subsequent values and measures the rate of change of the function. 
Spatial Differentiation
Differentiation measures the rate of change of a function.
Let’s consider a simple one dimensional example.
1st Derivative (cont…)
2nd Derivative
The formula for the 2nd derivative of a function is as follows:


Simply takes into account the values both before and after the current value. 
2nd Derivative (cont…)
1st & 2st Derivative (cont…)
Using Second Derivatives For Image Enhancement
The 2nd derivative is more useful for image enhancement than the 1st derivative
-	Stronger response to fine detail
-	Simpler implementation
-	We will come back to the 1st order derivative later on
The first sharpening filter we will look at is the Laplacian
-	Isotropic
-	One of the simplest sharpening filters
-	We will look at a digital implementation
The Laplacian
The Laplacian is defined as follows:


where the partial 1st order derivative in the x direction is defined as follows:


and in the y direction as follows:
The Laplacian (cont…)
So, the Laplacian can be given as follows:



We can easily build a filter based on this
The Laplacian (cont…)
Applying the Laplacian to an image we get a new image that highlights edges and other discontinuities
But That Is Not Very Enhanced!
-	The result of a Laplacian filtering is not an enhanced image.
-	We have to do more work in order to get our final image.
-	Subtract the Laplacian result from the original image to generate our final sharpened enhanced image.
Laplacian Image Enhancement
In the final sharpened image edges and fine detail are much more obvious.
Simplified Image Enhancement
The entire enhancement can be combined into a single filtering operation:
Simplified Image Enhancement (cont…)
This gives us a new filter which does the whole job for us in one step.
Variants On The Simple Laplacian
There are lots of slightly different versions of the Laplacian that can be used:
Variants On The Simple Laplacian
Variants On The Simple Laplacian
Two definitions of Laplacian: one is the negative of the other
Accordingly, to recover background features:



I: if the center of the mask is negative
II: if the center of the mask is positive
High Boost Filtering
High boost filtering:


I: if the center of the mask is negative
II: if the center of the mask is positive

When A=1, it is the standard Laplacian filtering, as the value of A increase past 1, the contribution of the sharpening process becomes less and less important.

High Boost Filtering
High Boost Filtering
1st Derivative Filtering
Implementing 1st derivative filters is difficult in practice.
For a function f(x, y) the gradient of f at coordinates (x, y) is given as the column vector:
1st Derivative Filtering (cont…)
The magnitude of this vector is given by:





For practical reasons this can be simplified as:

1st Derivative Filtering (cont…)
The simplest 1st Derivative Filtering:




Approximation: 

1st Derivative Filtering (cont…)
Roberts uses:



Approximation (Roberts Cross-Gradient Operators): 


1st Derivative Filtering (cont…)
There is some debate as to how best to calculate these gradients but we will use:


which is based on these coordinates

Sobel Operators
Based on the previous equations we can derive the Sobel Operators.




To filter an image it is filtered using both operators the results of which are added together.
Sobel Example







Sobel filters are typically used for edge detection
Sobel Example
Combining Spatial Enhancement Methods
-	Successful image enhancement is typically not achieved using a single operation.
-	Rather we combine a range of techniques in order to achieve a final result.
-	This example will focus on enhancing the bone scan to the right.
Combining Spatial Enhancement Methods (cont…)
Combining Spatial Enhancement Methods (cont…)
Combining Spatial Enhancement Methods (cont…)
Compare the original and final images
Summary
In this lecture we have looked at the idea of spatial filtering and in particular:
-	Basic of spatial filtering
-	Smoothing spatial filters
-	Smoothing linear filters
-	Order-statistics filters
-	Sharpening spatial filters
-	1st derivative filters
-	2nd derivative filters
Next time we will looking at other image enhancement methods in frequency domain.
## Image Enhancement: Filtering in the Frequency Domain
Contents
In this lecture we will look at image enhancement in the frequency domain:
-	The Fourier series & the Fourier transform
-	DFT Properties
-	Steps of Filtering in the Frequency Domain
-	Some Basic Frequency Domain Filters
-	Image smoothing
-	Image sharpening
The Big Idea

Introduction to 
Fourier Transform

f(x): continuous function of a real variable x. 
Fourier transform of f(x): 

The Discrete Fourier Transform (DFT)
How to calculate the DFT:
-	Step1: Let u=0, summing for all value of x.
-	Step2: Substitute u=u+1, and repeat the summation over all value of x.
-	Step 3: If u=N-1, terminate, otherwise, return to step 2.


The Discrete Fourier Transform (DFT)
-	The values of F(u) for each u are composed of the sum of all values of function f(x).
-	The values of f(x) are multiplied by sines and cosines of various frequencies.
-	Similar computation procedure in the inverse Fourier transform.
-	The domain (values of u) over which the values of F(u) range is appropriately called the frequency domain.
-	Each of the N terms of F(u) is called a frequency component of the transform.
The Discrete Fourier Transform (DFT)
Glass prism
-	Separates light into various color components.
-	Each depending on its wavelength (or frequency) content.
Fourier transform
-	Separates a function into various components.
-	Each depending on its frequency content.

The Discrete Fourier Transform (DFT)
The Discrete Fourier Transform of f(x, y), for x = 0, 1, 2…M-1 and y = 0,1,2…N-1, denoted by F(u, v), is given by the equation:



for u = 0, 1, 2…M-1 and v = 0, 1, 2…N-1.



Image DFT Example

DFT Properties
DFT Properties
DFT Properties
DFT Properties
DFT Properties
DFT Properties
The DFT of a two dimensional image can be visualised by showing the spectrum of the images component frequencies.
DFT Properties
DFT Properties
-	Features from an image can even sometimes be seen in the Fourier spectrum of the image.
-	The frequency component (u=v=0) corresponds to the average gray level of an image.
-	The low frequency correspond to the slowly varying components of an image.
-	An image of one room: smooth gray-level variations on the walls and floor.
-	The higher frequencies begin to correspond to faster and faster gray level changes in the image.
-	Edges, noise.
Steps of Filtering in the Frequency Domain
Steps:
-	Multiply the input image by (-1)x+y to center the transform.
-	Compute F(u,v) the DFT of the image in (1).
-	Multiply F(u,v) by a filter function H(u,v).
-	Compute the inverse DFT of the result.
-	Obtain the real part of the result in (4).
-	Multiply the result in (5) with (-1)x+y .
Steps of Filtering in the Frequency Domain
Filter H (u, v)
-	Suppresses certain frequencies 
-	Leaving others unchanged
-	G (u, v)=H( u, v) F (u, v)
-	The first element of H multiplies the first element of F,…
Zero-phase-shift filter
-	The value of H are always real
-	Each component of H multiplies both the real and the imaginary parts of F
-	Do not change the phase of the transform
Steps of Filtering in the Frequency Domain
The filtered image is obtained simply by taking the inverse Fourier transform of G(u,v):


The final image is obtained by:
1.Taking the real part of this result.
2.Multiplying by (-1)x+y to cancel the multiplication of the input image by this quantity.
Note: When the input image and the filter function are real, the imaginary components of the inverse transform should all be zero. In practice, the inverse DFT generally has parasitic imaginary components due to computational round-off errors. There component can be ignored.
Steps of Filtering in the Frequency Domain
Other steps include:
-	Cropping of the input image to its closest even dimensions.
-	Gray level scaling.
-	Conversion to floating point on input.
-	Conversion to an 8 bit integer format on the output.
-	Multiple filtering stages and other pre- and post-processing functions are possible.
-	There are numerous variations of above basic theme.
Some Basic Frequency Domain Filters
Some Basic Frequency Domain Filters
Some Basic Frequency Domain Filters
Filters Based on Gaussian Functions
-	Shapes are easily specified.
-	Forward and inverse Fourier transforms of a Gaussian function are real Gaussian functions. 



Filters Based on Gaussian Functions
Fourier transform pair
-	Both components of which are Gaussian and real.
-	The narrower the frequency domain filter:
-	The more it will attenuate the low frequencies
-	Resulting in increased blurring
-	In the spatial domain:
-	Implies a larger mask 
Filters Based on Gaussian Functions
Smoothing Frequency Domain Filters
-	Smoothing is achieved in the frequency domain by dropping out the high frequency components.
-	The basic model for filtering is:
	G(u,v) = H(u,v)F(u,v)
	where F(u,v) is the Fourier transform of the image being filtered and H(u,v) is the filter transform function.
-	Low pass filters - only pass the low frequencies, drop the high ones.
Smoothing Frequency Domain Filters
Three main smoothing frequency domain filters:
-	Ideal lowpass filter
-	Butterworth filter
-	Gaussian filter

These three filters cover the range from very sharp (ideal) to very smooth (Gaussian) filter functions.
Ideal Low Pass Filter
Simply cut off all high frequency components that are a specified distance D0 from the origin of the transform.





Changing the distance changes the behaviour of the filter. D0 is also called cutoff frequency.
Ideal Low Pass Filter (cont…)
The transfer function for the ideal low pass filter can be given as:



It is called 2D ideal low pass filter (ILPF)
where D(u,v) is given as:

Ideal Low Pass Filter (cont…)
Ideal Low Pass Filter (cont…)
Above we show an image, it’s Fourier spectrum and a series of ideal low pass filters of radius 5, 15, 30, 80 and 230 superimposed on top of it.
These circles enclose a percent of the image power, for a=92.0, 94.6, 96.4, 98, and 99.5%, respectively.
Ideal Low Pass Filter (cont…)
Ideal Low Pass Filter (cont…)
Ideal Low Pass Filter (cont…)
Butterworth Lowpass Filters
The transfer function of a Butterworth lowpass filter of order n with cutoff frequency at distance D0 from the origin is defined as:
Butterworth Lowpass Filter (cont…)
Butterworth Lowpass Filter (cont…)
Butterworth Lowpass Filter (cont…)
Butterworth Lowpass Filter (cont…)
Gaussian Lowpass Filters
The transfer function of a Gaussian lowpass filter is defined as:
Gaussian Lowpass Filters (cont…)
Lowpass Filters Compared 
Lowpass Filtering Examples
A low pass Gaussian filter is used to connect broken text: D0=80.
Lowpass Filtering Examples (cont…)
Different lowpass Gaussian filters used to remove blemishes in a photograph.
Sharpening in the Frequency Domain
-	Edges and fine detail in images are associated with high frequency components.
-	High pass filters - only pass the high frequencies, drop the low ones.
-	High pass frequencies are precisely the reverse of low pass filters, so:
   
   	Hhp(u, v) = 1 - Hlp(u, v)

Sharpening in the Frequency Domain
Sharpening in the Frequency Domain
Ideal High Pass Filters
The ideal high pass filter is given as:



where D0 is the cut off distance as before.
Ideal High Pass Filters (cont…)
Butterworth High Pass Filters
The Butterworth high pass filter is given as:









where n is the order and D0 is the cut off distance as before.
Butterworth High Pass Filters
Butterworth High Pass Filters (cont…)
Gaussian High Pass Filters
The Gaussian high pass filter is given as:


where D0 is the cut off distance as before.
Gaussian High Pass Filters (cont…)
High Pass Filter Comparison
High Pass Filter
Summary
In this lecture we looked at image enhancement in the frequency domain
-	The Fourier series & the Fourier transform
-	DFT Properties
-	Steps of Filtering in the Frequency Domain
-	Some Basic Frequency Domain Filters
-	Image smoothing
-	Image sharpening


## Image Restoration
Contents
In this lecture we will look at image restoration techniques used for noise removal
-	What is image restoration?
-	Noise and images
-	Noise models
-	Noise removal using frequency domain filtering
-	Estimating the degradation function
-	Inverse filtering
-	Weiner Filtering
What is Image Restoration?
Image restoration attempts to restore images that have been degraded
-	Identify the degradation process and attempt to reverse it
-	Similar to image enhancement, but more objective

Enhancement vs. Restoration
“Better” visual representation

Subjective

No quantitative measures


Mathematical 
Model dependent

Objective

Quantitative measures
Image Restoration General
-	One has to have some a priori knowledge about the degragation process.
-	Usually we need to know:
-	The noise in the original image
-	Model for degragation
-	Some information from original image

A Model of Image Degradation and Restoration
Degradation Model
Restoration Model
Restoration Model
-	The objective of restoration is to obtain an estimate            of the original image f(x,y).

-	Generally, the more we know about H, and    , the             will be closer to f(x,y).

-	The approach used throughout most of this chapter is based on various types of image restoration filters.
Noise and Images
The sources of noise in digital images arise during image acquisition (digitization) and transmission
-	Imaging sensors can be affected by ambient conditions
-	Interference can be added 
to an image during transmission
Noise Models
There are many different models for the image noise term η(x, y):
-	Gaussian
-	Most common model
-	Rayleigh
-	Erlang
-	Exponential
-	Uniform
-	Impulse
-	Salt and pepper noise
Noise Models
Noise Models
Noise Models
Noise Models
MATLAB Command
>Y = IMNOISE(X,'salt & pepper',p)
Noise Models
Noise Example
The test pattern to the right is ideal for demonstrating the addition of noise.
The following slides will show the result of adding noise based on various models to this image.
Noise Example (cont…)
Noise Example (cont…)
Periodic Noise
Periodic noise in an image arises typically from electrical or electromechanical interference during image acquisition.
Characteristics
-	Spatially dependent
-	Periodic - easy to observe in frequency domain
Processing method
-	Suppressing noise component in frequency domain
Periodic Noise
Estimation of Noise Parameters
-	Periodic noise typically are estimated by inspection of the Fourier spectrum of the image.
-	The parameters of noise PDFs may be known from sensor specifications.
-	Sometimes, it is necessary to estimate them for a particular imaging device.
-	When images generated by the sensor are available, frequently it is possible to estimate the parameters of the PDF from small patches of constant gray level.
Estimation of Noise Parameters
Estimation of Noise Parameters
Estimation of Noise Parameters
Estimation of Noise Parameters
Restoration of Noise Only Degradation
We can consider a noisy image to be modelled as follows:



where f(x, y) is the original image pixel, η(x, y) is the noise term and g(x, y) is the resulting noisy pixel.
Restoration of Noise Only Degradation
-	If the noise is known, we can just subtract them from g(x,y) or G(u,v) to restore f(x,y) or F(u,v).
-	In the case of periodic noise, it is possible to estimate N(u,v) from the spectrum of G(u,v), then N(u,v) can be subtracted from G(u,v) to obtain an estimate of the original image.
-	Usually, the noise is unknown, so subtracting them from g(x,y) or G(u,v) is not a realistic option.
-	When only additive noise is present, spatial filtering is a choice. In this case, enhancement and restoration become almost the same.
Periodic Noise Reduction by Frequency Domain Filter
Here we will look at more specialized frequency domain filters, which are efficient to reduce or remove periodic noise. They are:
-	Bandreject filter
-	Bandpass filter
-	Notch filter
Periodic Noise
Band Reject Filters
An ideal band reject filter is given as follows:










W is the band width, D0 is its ring center.
Band Reject Filters
Butterworth bandreject filter of order n:




Gaussian bandreject filter:
Band Reject Filters (cont…)
The ideal band reject filter is shown below, along with Butterworth and Gaussian versions of the filter
Band Reject Filter Example
Band Pass Filter
Band Pass Filter Example
Notch Filter
Notch Filter
Notch Filter
Notch Filter
Notch Filter
Notch Filter
Notch Filter
Estimating the Degradation Function
Three principal ways to estimate 
-	Observation
-	Experimentation
-	Mathematical modeling

-	Using a degradation function that has been estimated in some way to restore a image is called Blind deconvolution.
-	Because the true degradation function is seldom known completely.

Estimation by Image Observation

-	Given an image, without prior knowledge.
-	Gather information from the image itself.
-	Example: the image is blurred
-	Look at a small section of the image.
-	Look for areas of strong signal content.
-	Construct an unblurred image of the same size and characteristics as the observed subimage.

Estimation by Image Observation

Let the observed subimage be denoted by gs(x,y).
Let the constructed subimage be denoted by Assuming the effect of noise is negligible (strong -signal area) , then



Deduce the complete function H (u, v).
Eg. Suppose that a radial plot of Hs(u,v) turns out to have the shape of Butterworth lowpass filter. We can use that information to construct a function H(u,v) on a larger scale, but having the same shape.

Estimation by Experimentation

Assume: 
-	Equipment similar to another equipment
-	Obtain an accurate estimate of the degradation
Images similar to the degraded image
-	Various system setting 
-	Until degraded as closely as possible to the image we wish to restore.
-	Space-invariant system with impulse response

Estimation by Experimentation

-	Impulse response of the degradation
-	Imaging an impulse using the same system settings.
-	An impulse 
-	Is simulated by a bright dot of light.
-	Bright enough to reduce the effect of noise.
-	Fourier transform of an impulse is a constant.



Estimation by Experimentation


Estimation by Modeling

Consider environmental conditions causing degradations
-	Example: atmospheric turbulence.  
-	Hufnagel and Stanley 1964.

Modeling:



Where k is a constant, and depends on the nature of the turbulence.
Has the same form of Gaussian lowpass filter, it is used sometimes to model mild, uniform bluring.
Atmospheric Turbulence

Estimation by Modeling

Derive a mathematical model of Motion Blur
-	An image blurred by uniform linear motion between Image and the sensor during image acquisition
-	Image f(x,y) is in planar motion 
-	x0(t) and y0 (t) time varying components of motion
Motion Blur
Motion Blur
Motion Blur
Inverse Filtering
Studying restoration of images degraded by a degradation function H, which is given or obtained by a method, such as those discussed in the previous sections.



It tells us that even if we know the degradation function we cannot recover the undegraded image. Because N(u,v) is a random function whose Fourier transform is not known.
Inverse Filtering
Besides, if the degradation has zero or very small values, then the ratio N(u,v)/H(u,v) could easily dominate the estimate F(u,v). In fact, this is frequently the case.

Then how to deal with this situation?
-	Limit the filter frequencies to values near the origin.
-	H(0,0): 
-	Is equal to the average value of h (x, y).
-	Highest value of H(u,v) in the frequency domain.
-	Reduce the probability of encountering zero values.
Inverse Filtering
Wiener Filtering
Inverse filtering approach makes no explicit provision for handling noise.

Wiener filtering incorporate the information:
-	Degradation function
-	Statistical Characteristics of noise

Also called Minimum Mean Square Error Filtering.
Wiener Filtering
Consider images and noise as random processes.
Objective is to find an estimate     of the uncorrupted image f.
Mean square error between them is minimized:



􀂄  is the expected value of the argument.

Wiener Filtering
Assumption:
-	Noise and image are uncorrelated.
-	One or the other has zero mean.
-	The gray levels in the estimate are a linear function of the levels in the degradation image.
Wiener Filtering
Wiener Filtering
Wiener Filtering
-	Spectrally white noise, the spectrum |N(u,v)|2 is a constant.
-	Power spectrum of the undegraded image seldom is known.
-	When these quantities are not known or cannot be estimated, we obtain approximation:

Wiener Filtering
Wiener Filtering
Wiener Filtering
Summary
In this lecture we looked at image restoration for noise removal
-	Restoration is slightly more objective than enhancement
-	Frequency domain techniques are particularly useful for removing periodic noise
-	Inverse filtering
-	Weiner Filtering
## Morphological Image Processing
Contents
In this lecture we will consider
-	What is morphology?
-	Basic Concepts of Set Theory
-	Dilation and Erosion
-	Opening and Closing
-	Hit or Miss Transform
What Is Morphology?
-	Presented by J. Serra in 1964.
-	Morphological image processing (or morphology) describes a range of image processing techniques that deal with the shape (or morphology) of features in an image.
-	The basic idea of Morphology is to use a special structuring element to measure or extract the corresponding shape or characteristics in the input images for further image analysis and object recognition.
-	The mathematical foundation of morphology is the set theory.
-	In this chapter, the input images are binary images.
0, 1, Black, White?
-	Throughout all of the following slides whether 0 and 1 refer to white or black is a little interchangeable
-	All of the discussion that follows assumes that images are made up of 0s for background pixels and 1s for object pixels (foreground)
-	After this it doesn’t matter if 0 is black, white, yellow, green…….

A is a set, if a=(a1,a2) is an element of A, then, 
aA
If not, then, aA

: null (empty) set

Typical set specification: 
      C={w|w=-d, for d D}
Basic Concepts of Set Theory
-	A subset of B: AB
-	Union of A and B: C=AB
-	Intersection of A and B: D=AB
-	Disjoint sets: AB= 
-	Complement of A: Ac={w|wA}
-	Difference of A and B: 
A-B={w|wA, wB}=A Bc


Structuring Element, Hit, Fit, & Miss
Fit: for each of SE pixels set to 1, the corresponding image pixel is also 1.
Hit: at least one of SE’s pixels set to 1 the corresponding image pixel is also 1.
Miss: no pixel in the structuring element covers an on pixel in the image.
Fit, Hit, & Miss
Structuring Elements
-	Structuring elements can be any size and make any shape.
-	Usually, element values are 0,1 and none(x).
-	Structural Elements have an origin.
-	For simplicity we usually use rectangular structuring, then we can take the middle pixel as its origin.
-	Empty spots in the Structuring Elements are don’t care’s!

Fundamental Operations
-	Fundamentally morphological image processing is very like spatial filtering.
-	The structuring element is moved across every pixel in the original image to give a pixel in a new processed image.
-	The value of this new pixel depends on the operation performed.
-	There are two basic morphological operations: dilation and erosion.
Dilation
Dilation of image  f  by structuring element  s is given by f     s
The structuring element s is positioned with its origin at (x, y) and the new pixel value is determined using the rule:

Example for Dilation
Example for Dilation
Example for Dilation
Example for Dilation
Example for Dilation
Example for Dilation
Example for Dilation
Example for Dilation
Dilation Example
Dilation Example
Dilation
Dilation:
-	B is often called the “structuring element”
-	Process consists of obtaining the reflection of B, about its origin
-	Then shifting this reflection, B, by x
-	The dilation of A by B is the set of all x, displacements such that B and A overlap by at least one element
Dilation Example
Dilation Example

Dilation Example 1
Dilation Example 2
What Is Dilation For?
Dilation can repair breaks



Dilation can repair intrusions



Watch out: Dilation enlarges objects
Erosion
Erosion of image f by structuring element s is given by f  s
The structuring element s is positioned with its origin at (x, y) and the new pixel value is determined using the rule:

Example for Erosion
Example for Erosion
Erosion Example
Erosion Example
Erosion
Erosion Example


Erosion Example 1
Erosion Example 2
What Is Erosion For?
Erosion can split apart joined objects




Erosion can strip away extrusions



Watch out: Erosion shrinks objects
Erosion & Dilation Application
Compound Operations 
More interesting morphological operations can be performed by performing combinations of erosions and dilations
The most widely used of these compound operations are:
-	Opening
-	Closing
	Opening
	The opening of image f by structuring element s, denoted f ○ s is simply an erosion followed by a dilation
	 f ○ s = (f s)    s


Opening Example
Opening Example
Opening Example
Opening
Opening Example
Closing
The closing of image f by structuring element s, denoted f - s is simply a dilation followed by an erosion
	 f - s = (f    s)s


Closing Example
Closing Example
Closing Example
Closing
Closing Example


In essence, dilation expands an image and erosion shrinks it.

Opening:
-	generally smoothes the contour of an image, breaks thin connections, eliminates protrusions.

Closing:
-	smoothes sections of contours, but it generally fuses breaks, holes, gaps, etc.
Morphological Processing Example
Morphological Processing Example









Summary
-	The purpose of morphological processing is primarily to remove imperfections added during segmentation.
-	The basic operations are dilation and erosion.
-	Using the basic operations we can perform opening and closing.
-	More advanced morphological operation can then be implemented using combinations of all of these.

## Morphological Image Processing--new
Morphological Algorithms
We can begin to consider some more interesting morphological algorithms by using the simple morphological technique we have learnt. 

We will look at:
-	Boundary extraction
-	Region filling
There are lots of others as well though:
-	Extraction of connected components
-	Convex Hull
-	Thinning/thickening
-	Skeletons
Boundary Extraction
-	Extracting the boundary (or outline) of an object is often extremely useful
-	The boundary can be given simply as
	                    β(A) = A - (AB)
	                Boundary Extraction Example
	                A simple image and the result of performing boundary extraction using a square 3*3 structuring element
	                Boundary Extraction Example
	                Region Filling
	                Given a pixel inside a boundary, region filling attempts to fill that boundary with object pixels



Region Filling (cont…)
The key equation for region filling is:

Where X0 is the starting point inside the boundary, B is a simple structuring element and Ac is the complement of A.
This equation is applied repeatedly until:
                              Xk =Xk-1.
Finally the result is unioned with the original boundary: Xk A.
Region Filling Step By Step
Region Filling Step By Step



Region Filling Step By Step



Region Filling Step By Step



Region Filling Step By Step



Region Filling Step By Step



Region Filling Step By Step



Region Filling Step By Step



Region Filling Step By Step



Region Filling Step By Step



Region Filling Step By Step



Region Filling Step By Step



Region Filling Step By Step



Region Filling Step By Step



Region Filling Step By Step



Region Filling Step By Step



Region Filling Step By Step



Region Filling Step By Step



Region Filling Step By Step
Region Filling Step By Step
Region Filling Example


Extraction of Connected Components



Extraction of Connected Components



Extraction of Connected Components



Extraction of Connected Components



Extraction of Connected Components



Extraction of Connected Components



Extraction of Connected Components



Extraction of Connected Components



Extraction of Connected Components



Extraction of Connected Components



Extraction of Connected Components











Thinning

Thinning
Thinning
Thinning
Thinning
Thinning





Skeletons

Grayscale Morphology
Grayscale Erosion and Dilation

Grayscale Erosion and Dilation
Grayscale Erosion and Dilation
Grayscale Erosion and Dilation
Grayscale Erosion and Dilation
Grayscale Erosion and Dilation
flat
Grayscale Erosion and Dilation
Grayscale Erosion and Dilation
Grayscale Erosion and Dilation
-	Gray-scale X-ray image of size 448 425 × pixels. 
-	Erosion (a) using a flat disk SE with a radius of 2 pixels. 
-	Dilation (a) using the same SE. 
Grayscale Erosion and Dilation
The eroded grayscale image b) was darker than the original a), that the sizes of bright features were reduced, and that the sizes of dark features is increased.

Grayscale Erosion and Dilation
The effects of c) are the opposite of using erosion. The bright features were thickened and the intensities of the darker features were reduced. 
Grayscale Erosion and Dilation
Matlab code:
Grayscale Erosion and Dilation
Grayscale Erosion and Dilation
Grayscale Erosion and Dilation
Grayscale Opening and Closing
The expressions for opening and closing grayscale images have the same form as their binary counterparts. 
Grayscale Opening and Closing
Grayscale Morphological Smoothing
Grayscale Morphological Gradient

## Image Segmentation1
Contents
So far we have been considering image processing techniques used to transform images for human interpretation.
Today we will begin looking at automated image analysis by image segmentation:
-	The segmentation problem
-	Edges detection 
-	Edge linking and boundary detection
-	Hough Transform

Segmentation
Segmentation
Segmentation

Detection Of Discontinuities
There are three basic types of grey level discontinuities that we tend to look for in digital images:
-	Points
-	Lines
-	Edges
We typically find discontinuities using masks and correlation.

Edge Detection
Spatial Differentiation
2nd Derivative
The formula for the 2nd derivative of a function is as follows:


Edges & Derivatives
We can see
how derivatives 
are used to find 
discontinuities
1st derivative tells us 
where an edge is.
2nd derivative can
be used to show 
edge direction.


Derivatives & Noise





Common Edge Detectors
Given a 3*3 region of an image the following edge detection filters can be used
Common Edge Detectors
Edge Detection Example
Edge Detection Example
Edge Detection Example
x=imread(name);
x1=double(x);   imshow(x);
[m,n]=size(x);
sm=ones(5,5);
x1=filter2(sm,x1); 

sobelx=[-1 -2 -1; 0 0 0; 1 2 1];
sobely=[-1 0 1; -2 0 2; -1 0 1];

xx=filter2(sobelx,x1); figure, imshow(xx, []);
yy=filter2(sobely,x1); figure, imshow(yy, []);

x2=abs(xx)+abs(yy);
figure, imshow(x2, []);
th=sum(sum(x2))/(m*n);





Laplacian of Gaussian (LoG)
Laplacian Of Gaussian
Remarks:
-	Because the second derivative is a linear operation, convolving an image with LoG is the same as convolving the image with the Gaussian smoothing function first, and then computing Laplacian.
-	The purpose of Gaussian in the LoG is to smooth the image.
-	The purpose of  Laplacian is to establish the location of edges (zero crossing)
Laplacian Of Gaussian
The Laplacian of Gaussian (LoG, or Mexican hat) filter uses the Gaussian for noise removal and the Laplacian for edge detection

Operators in MATLAB
I = imread('Fig0926(a)(rice).tif');
BW1 = edge(I,'sobel');   %Sobel operators
BW2 = edge(I,'roberts');  %Roberts operators
BW3 = edge(I,'prewitt'); %Prewitt operators
BW4 = edge(I,'log'); %LOG operators
subplot(2,3,1),imshow(I), title('Original Image')
subplot(2,3,2),imshow(BW1), title('Sobel')
subplot(2,3,3),imshow(BW2), title('Roberts')
subplot(2,3,4),imshow(BW3), title('Prewitt')
subplot(2,3,5),imshow(BW4), title('LOG')
Optimal Edge Detection: Canny
John Canny: 
A Computational Approach to Edge Detection, IEEE Transactions on Pattern Analysis and Machine Intelligence ( Volume: PAMI-8, Issue: 6, Nov. 1986 ).


Optimal edge detector should have 3 properties:
-	Good SNR: Filter responds to edge, not noise.
-	Good Localization: detected edge near true edge. 
-	Single Response: one per edge.


Optimal Edge Detection: Canny

1. Gaussian function: noise removing



2. 1st derivative (soble, Prewitt or Roberts…)


3. Non-maxima Suppression (NMS):  It is not enough to confirm the edge  by the global gradient. We can keep the local maxima gradient to find the edge (find the max gradient alone the angle, it is the edge point).
   
4. Double threshold：
-	Usually, single threshold cannot get good results, since the perfect threshold is difficult to choose.
-	The double thresholds are the better way: th1, th2, th1=0.4th2
-	Then we can get two edge image: I1, I2, the I2 is gotten by the bigger threshold th2, which has removed more noise, and also loses some useful edge. I1 contains more detail edge, so we can combine them together.



Optimal Edge Detection: Canny
Edge Link and Boundary Detection
Local Processing
Local Processing
Local Processing
Global Processing
Global Processing
-	Find lines, curves, or parts of lines or curves in an input image. 
-	Such an image might be the output of an edge detector discussed in the previous lectures.
-	Model fitting - Given a group points, find the curve or line that explains the data points best.
-	For example: Line.

Hough Transform

Hough Transform
Calculating steps of hough transform:
-	In parameter space, quantize a and b, and give out (amin,amax) and (bmin,bmax).
-	Set an accumulator A: A is A(amin:amax, bmin:bmax),set A to zero at the beginning.
-	For a given point (xi,yi) in the original space, let a equal to each allowed values on the a-axis, and solve for the corresponding b using the equation: 
-	The resulting b are then rounded off to the nearest allowed value in the b-axis. If a choice of ap results in solution bq, we let A(p,q)=A(p,q)+1
-	At the end of this procedure, a value Q in A(i,j) corresponds to Q points in the xy space lying on the line y=aix+bj.
Hough Transform
Hough Transform
Hough Transform
Hough Transform
Hough Transform
Hough Transform
Hough Transform
Hough Transform
Hough Transform
Hough Transform
Hough Transform
Hough transform detecting circles
Although the focus so far has been on straight lines, the hough transform is applicable to any function of the form g(v,c)=0, where v is a vector of coordinates and c is a vector of coefficients. For example, the points lying on the circle


can be detected by using the approach just discussed.
The only difference is that there 
are three parameter.
Hough transform detecting circles
-	The procedure is to increment c1 and c2, solve for the c3 that satisfy:                      



-	Then updated the accumulator corresponding to the cell associated with the triplet (c1,c2,c3)

-	Clearly, the complexity of the hough transform is proportional to the number of coordinates and coefficients in a given functional representation


Hough transform detecting circles
Hough transform detecting circles
Find the center of the circle (the biggest value in the accumulator), since the radius is known, then we can find the circle we want.

Hough transform detecting circles
Hough Transform
-	The original Hough transform was designed to detect straight lines and curves. 
-	This method can be used if analytic equations of object borderlines are known. 
-	Since each point is handled independently, parallel implementations are possible.
-	It becomes difficult when the dimension of the parameter space is large.
-	Now hardware of Hough transform for line detection is available.
Summary
Next lecture we will look at:
-	Thresholding
-	Region based segmentation
-	Morphological watersheds approach

## Image Segmentation2
Contents
Today we will begin looking at automated image analysis by examining the thorny issue of image segmentation:
-	Thresholding
-	Region based segmentation
-	Morphological watersheds approach
Thresholding
-	Thresholding is usually the first step in any segmentation approach
-	We have talked about simple single value thresholding already
-	Here we will look at:
-	Basic Global Thresholding 
-	Basic Adaptive Thresholding
-	Minimisation Thresholding




Thresholding
Thresholding
Basic Global Thresholding
Basic Global Thresholding
Threshold2.m
Basic Global Thresholding
Basic Global Thresholding Algorithm
The basic global threshold, T, is calculated as follows:
-	Select an initial estimate for T (typically the average grey level in the image).
-	Segment the image using T to produce two groups of pixels: G1 consisting of pixels with grey levels >T and G2 consisting pixels with grey levels ≤ T.
-	Compute the average grey levels of pixels in G1 to give μ1 and G2 to give μ2.
-	Compute a new threshold value:


-	Repeat steps 2 - 4 until the difference in T in successive iterations is less than a predefined limit T0.




Basic Global Thresholding Algorithm
Remarks:
-	When the background and object occupy comparable areas in the image, a good initial value for T is the average gray level of the image.
-	When objects are smaller than the background (or vice versa), then one group of pixels will dominate the histogram and the average gray level is not as good an initial choice.
-	A more appropriate initial value for T in cases such as this is the middle value.
Basic Global Thresholding Algorithm
Single Value Thresholding and Illumination
Uneven illumination can really upset a single valued thresholding scheme.
Basic Adaptive Thresholding
-	An approach to handling situations in which single value thresholding will not work is to divide an image into sub images and threshold these individually.
-	Since the threshold for each pixel depends on its location within an image this technique is said to adaptive.
Basic Adaptive Thresholding Example
The image below shows an example of using adaptive thresholding with the image shown previously.




As can be seen success is mixed.
But, we can further subdivide the troublesome sub images for more success.
Basic Adaptive Thresholding Example
Basic Adaptive Thresholding Example (cont…)
These images show the 
troublesome parts of the 
previous problem further 
subdivided.
After this sub division 
successful thresholding 
can be achieved.
Otsu
Proposed by Nobuyuki Otsu (大津展之）
Otsu
Otsu
-	The mean of the image is M。
-	Now, there is a given threshold t, which can segment the image into two parts: Object and background:
-	The probability of group object is po(t)(前景像素点数占整幅图像的比例), mean is MO
-	The probability of group background is pb(t) (背景像素点数占整幅图像的比例),  mean is MB

The intra-class variance of Otsu： 

			ICV=po(t)∗(MO−M)2+pb(t)∗(MB−M)2

Object: find a threshold that makes the maximum ICV.
Otsu
Try all t to find the maximum ICV:
Otsu
Segmentation result:





			ICV=po(t)∗(MO−M)2+pb(t)∗(MB−M)2
Sometimes, when the sizes of Object and Background have big difference, it doesn’t work. 
We can consider to modify the criterion:
			ICV=po(t)a∗(MO−M)2+pb(t)a∗(MB−M)2

Otsu
对图像中仅有目标-背景类的图像，Otsu可以得到统计意义上的最优分割。






Otsu
Otsu
Problems With Single Value Thresholding
Single value thresholding only works for bimodal histograms
Images with other kinds of histograms need more than a single threshold
Problems With Single Value Thresholding (cont…)
Region based segmentation
Region based segmentation
Region Growing
Region Growing:
-	As name implies, region growing is a procedure that groups pixels or sub-regions into larger regions based on predefined criteria. 
-	Select a group start (seed) points.
-	Grow the point based on a certain criteria.
-	The selection of criteria depends not only on the problem under consideration, but also on the type of image data available. For example:
-	Land-use satellite imagery: depend heavily on the use of color
-	Monochrome image: depends on set of descriptors based on gray levels and spatial properties (moments or texture).
-	Connectivity should be considered.
-	Another problem in region growing is the formulation of a stopping rule. Basically, growing a region should be stop when no more pixels satisfy the criteria in that region.
Region Growing
Region Growing:
-Determine seed points to maximum gray level.
-Growing criteria:
-	Gray level value difference (with respect to Seed Points) less than a threshold.
-	Each candidate pixel should be N8 of region.
Region Growing
Region Growing
Region Growing


Region Splitting and Merging
Region Splitting and Merging
Region Splitting and Merging
Morphological Watersheds 
Morphological Watersheds 
Morphological Watersheds 
Morphological Watersheds 
Morphological Watersheds 
Morphological Watersheds 
Morphological Watersheds 
Morphological Watersheds 
Morphological Watersheds 
Morphological Watersheds 
Morphological Watersheds 
Dam Construction
Dam Construction
Dam Construction
Dam Construction
Dam Construction
Dam Construction
Watershed Segmentation Algorithm
Watershed Segmentation Algorithm
Watershed Segmentation Algorithm
Watershed Segmentation Algorithm
Watershed Segmentation Algorithm
Watershed Segmentation Algorithm
Watershed Segmentation Algorithm
Watershed Segmentation Algorithm
Watershed Segmentation Algorithm
Watershed Segmentation Algorithm
Watershed Segmentation Algorithm
Summary
There are many other segmentation technologies:
-	Susan Operators
-	Snake modal
-	Global Hough Transform
-	….


## Color Image Processing1
Contents 
-	Color fundamentals 
-	Color models
-	Pseudo color
-	Intensity slicing
-	Gray level to color transform
-	Multispectral color models 




Color Fundamental
Human retinas have two types of photoreceptors
-	Cones
-	As noted in Section 2.1.1, cones are the sensors in the eye responsible for color vision
-	High light level, high acuity vision
-	Each type of cone has a different spectral response
-	Rods
-	Rods are more spread out and are sensitive to low levels of illumination
Color Fundamental
Color Fundamental
Color Fundamental



The characteristics used to distinguish one color from another are:
-	Hue
-	Hue represents dominant color as perceived by an observer.
-	An attribute associated with the dominant wavelength in a mixture of light waves.
-	Thus, when we call an object red or yellow, we are specifying its hue.
-	Saturation
-	The pure spectrum colors are fully saturated, colors such as pink (red and white) are less saturated.
-	Saturation being inversely proportional to the amount of white light added.
-	Brightness (Intensity)
-	Embody the chromatic notion of intensity






RGB Image
RGB Image



CMY and CMYK color models——For printer
-	Cyan (青), magenta (深红), and yellow (黄) are the secondary colors of light. Or the primary colors of pigments.
-	Assumpting all color values have been normalized to the range [0, 1].


CMYK color models
-	K is true black.
-	Equal amounts of pigment primaries should produce black.
-	In practice, combining these colors for printing produces a muddy looking black.
-	So a fourth color k is added to produce true black.
-	So called four-color printer.


The conversion from CMY to CMYK begins by letting: 


The conversions from CMYK back to CMY are:
C = C*(1−K)+K
M = M*(1−K)+K
Y = Y*(1−K)+K






Point remove up or down



Converting colors from RGB to HSI (R,G,B are normalized)

Converting colors from HSI to RGB 


YIQ and YUV Color Model
YIQ is the color model used for color TV in America (NTSC) . 
Y is luminance, I & Q are color (I=red/green, Q=blue/yellow).
YUV is the color model used for color TV in Israel (PAL), and in video.
Y is luminance, U and V are blue and red as in YIQ.

When processing colour images, the following problems have to be dealt with:
-	The images are vectorial → 3 or 4 numbers are associated with each pixel.
-	The colours recorded by a camera are heavily dependent on the lighting conditions.


The lighting conditions of the scene have a large effect on the colours recorded.
Problem in Color Image
Knowing just the RGB values is not enough to know everything about the image.
-	The R, G and B primaries used by different devices are usually different.
For scientific work, the camera and lighting should be calibrated.
Pseudo Color Processing
-	The principle of pseudo color is for human visualization and interpretation of gray scale events in an image.
-	Pseudo color processing
-	Intensity slicing
-	Gray level to color transform
-	Multispectral color models












Summary
In this lecture we have learned:
-	Color Fundaments
-	Color Model
-	Pseudo-color processing

Next we will look at:
-	Full-color transformation
-	Smoothing and Sharpening
-	Color Slicing
-	Noise in color images
## Color Image Processing2
Contents 
Full-color processing
-	Color transformations
-	Smoothing and sharpening
-	Color segmentation
-	Noise in Color Image

A full color image has a vector at each pixel. For colour images, these vectors each have 3 or 4 components.
There are two Categories to process vectorial images:
First Category
-	Process each component image individually
-	Form a composite processed color image
Second Category
-	Work with color pixel directly.
-	Color pixels are vectors.


Each channel is processed separately:
Full Color Image Processing
The colour triplets are processed as single units:







Color Transformation



Color Complements





Color Slicing



Tone and Color Corrections
Application Examples: Face and tongue diagnosis
Tone and Color Corrections
Tone and Color Corrections
Saturation Enhancement
-	减小饱和度，图像颜色变淡，原来比较淡的区域已经变为灰色
-	增加饱和度，图像颜色变艳


Hue Enhancement
与亮度、饱和度的增强相比，色调增强比较特殊。
在HSI空间中，色调对应的是角度，而且是循环的。
如果色调整体加或减一个数，颜色在色谱上移动。当数字比较小时，色调变冷或暖；当数字较大时，色调会出现剧烈变化。


Histogram Processing

























Noise in Color Images
Summary
In this lecture we have learned:
-	Color Fundaments
-	Color Model
-	Pseudo-color processing
-	Full-color transformation
-	Smoothing and Sharpening
-	Color Slicing
-	Noise in color images
Next we will look at Representation & Description


## Representation & Description(Feature Extraction)
Luguangm@gmail.com
Contents
This lecture will cover:
-	Over view
-	Representation
-	Boundary descriptor
Overview
-	After an image has been segmented into regions. Representation and description should be considered.
-	Representation and description used to make the data useful to a computer. 
-	Representing region in 2 ways:
-	In terms of its external characteristics (its boundary)  focus on shape characteristics.
-	In terms of its internal characteristics (its region)  focus on regional properties, e.g., color, texture.
-	Sometimes, we may need to use both ways.
Overview
-	Choosing a representation scheme, however is only the part of the task of making data useful to computer.
-	The next task is to describe the region based on the chosen representation.
-	For example: 
-	Representation  boundary.
-	Description  length of the boundary, orientation of the straight line joining its extreme points, and the number of concavities in the boundary.
Sensitivity
Feature selected as descriptors should be as insensitive as possible to variations in 
-	size
-	translation
-	rotation
Following descriptors satisfy one or more of these properties.
Representation
-	Segmentation techniques yield raw data in the form of pixels along a boundary or pixels contained in a region.
-	These data sometimes are used directly to obtain descriptors.
-	Standard uses techniques to compute more useful data (descriptors) from the raw data in order to decrease the size of data.
Chain Codes
Chain Codes
Unacceptable because
-	The resulting chain of codes tends to be quite long.
-	Any small disturbances along the boundary due to noise or imperfect segmentation cause changes in the code that may not be related to the shape of the boundary.

Chain Codes
Solve the problems by
-	Resample the boundary by selecting a larger grid spacing.
-	Then, as the boundary is traversed, a boundary point is assigned to each node of the large grid, depending on the proximity of the original boundary to that node.
Chain Codes
Chain Codes
Chain Codes
Chain Codes
Chain Code Smoothing
Chain Code Smoothing
Chain Code Smoothing
Polygonal Approximations
-	Boundary can be approximated with arbitrary accuracy by a polygon.
-	For a closed curve, the approximation is exact when the number of the segments in the polygon is equal to the number of points in the boundary.
-	The goal: Try to capture the “essence” of the boundary shape with the fewest possible polygonal segments.
-	Not trivial and time consuming.
-	Three methods:
-	Minimum perimeter polygons
-	Merging techniques
-	Splitting techniques
Minimum Perimeter Polygons
Minimum Perimeter Polygons
Minimum Perimeter Polygons
Merging Techniques
Merging Techniques
Splitting techniques
Splitting techniques
Landmark Points
Landmark Points
Signatures
Signatures
Signatures
Boundary Segments
Boundary Segments
-	The set different H-S is called convex deficiency D (凸形缺陷) of the set S.
Boundary Segments
-	The region boundary can be partitioned by following the contour of S and marking the points at which a transition is made into or out of a component of the convex deficiency.
-	In practice, digital boundaries tend to be irregular because of digitization, noise. These effect usually result in convex deficiency:
-	To smooth a boundary prior to partitioning.
-	Use a polygonal approximation first.

Skeletons


Skeletons
Skeletons
Skeletons
Skeletons
Skeletons
Skeletons
Skeletons




Boundary Descriptors
Position
Direction
Enclose Area
Minimum Enclosing Rectangle
Minimum Enclosing Rectangle
Curvature
-	The rate of change of slope
-	Difficult to do as digital boundaries tend to be locally “ragged”
-	Using the difference between the slopes of adjacent boundary segments (which represented as straight lines)
-	E.g.: k1, and k2 are the slopes of two neighbor lines, and then the curvature at point a is k1-k2.
Curvature
-	As the boundary if traversed in the clockwise direction, a vertex point p is said to be the part of convex if the change in slope at p is nonnegative. Otherwise, p is said to belong to a concave.
-	Line (if the change is less than 10 degrees) and corner (changes exceeds 90 degrees)
Summary
We have looked at:
-	Over view
-	Representation
-	Boundary descriptor
Minimum Enclosing Rectangle
Minimum Enclosing Rectangle


## Representation2
Luguangm@gmail.com
Contents
This lecture will cover:
-	Boundary Descriptors
-	Region descriptor
-	Some simple descriptor
-	Topological descriptor
-	Texture
-	Moments of two-dimensional function
-	Relational descriptor
-	Color descriptor

Fourier Descriptors
Fourier Descriptors
Fourier Descriptors
Fourier Descriptors
Fourier Descriptors
Regional Descriptors
-	Some simple descriptor
-	Topological descriptor
-	Texture
-	Moments of two-dimensional function
Simple Descriptors
-	Area = the number of pixels in the region
-	perimeter = length of its boundary
-	Compactness = (perimeter)2/area








-	Other simple measures used as region descriptor includes: the mean and median of the gray levels, the minimum and maximum gray levels, and the number of pixels with values above or blow the mean.
Simple Descriptors
Simple Descriptors
Simple Descriptors
Simple Descriptors
Topological Descriptors
Topological Descriptors
Topological Descriptors
Topological Descriptors














Texture
Moments of Two Dimensional Functions
-	Moments can be used to represent the geometrical characters in image. It is also called moment invariants, because it is insensitive to rotation, translation, and scale.
-	Moment invariants (proposed by Hu in 1965 in paper: Visual pattern recognition by moment invariants.




Moments of Two Dimensional Functions
Code of Hu’s Moment
function HuF = Hu_moment(image)
image=double(image);
m00=sum(sum(image));
m10=0;m01=0;m11=0;m20=0;m02=0;m21=0;m12=0;m30=0;m03=0; ux=0;uy=0;
[row,col]=size(image);
for i=1:row
    for j=1:col
        m10=m10+i*image(i,j);
        m01=m01+j*image(i,j);
        m11=m11+i*j*image(i,j);
        m20=m20+(i*i)*image(i,j);
        m02=m02+(j*j)*image(i,j);
        m21=m21+(i*i)*j*image(i,j);
        m12=m12+i*(j*j)*image(i,j);
        m30=m30+(i*i*i)*image(i,j);
        m03=m03+(j*j*j)*image(i,j);
    end
end
ux=m10/m00; uy=m01/m00;

n00=m00;  
n10=0; 
n01=0;
n11=m11-uy*m10;
n20=m20-ux*m10;
n02=m02-uy*m01;
Moments of Two Dimensional Functions
Moments of Two Dimensional Functions
Relational descriptors
-	We introduced rewriting rules for describing texture.
-	Here, we expand that concept in the context of relational descriptors.
-	Capture the basic repetitive patterns in a boundary or region based on rewriting rules.
-	Directed line segments
-	Tree


Relational descriptors
Tree descriptors
-	Definition 
-	A unique root node.
-	The remaining nodes are partitioned into m disjoined sets T1,…, T m (subtree)
-	The tree frontier(末梢): the set of nodes at the bottom of the tree (the leaves)

Color descriptors
-	Color histogram descriptor
-	A color histogram is a 3D entity where each pixel of an image (rather than each sample) is placed into a bin.
-	The color space is divided into volumetric bins each of which represent a range of colors.
-	Each axis of the color space may be divided independently of the others. This allows the axes to have different resolutions.
-	In RGB may want to allocation more resolution in G than R or B
-	In HSI may want to allocate more resolution on S than H or I
Color descriptors
Color descriptors
Color descriptors
-	HSV Model
   Color descriptors
   RGB to HSV:
   Color descriptors
   We can find from the HSV space: 
   Hue: [21, 40]->Orange，[41, 75] ->Yellow, [76, 155] ->Green, [156, 190] ->Cyan, [19l, 270] ->Blue, [271, 295] ->Purple, [296, 315] ->Magenta, [316, 20] ->Red.
   Saturation: [0, 0.2] -> human eye cannot identify hue, appear to gray, (0.2, 0.7]->we can see color, (0.7, 1]->Hue keeps slight changes. 
   Value: [0, 0.2] -> appear to black, (0.2, 0.7]->noted the changes, (0.7, 1]->appear to white. 
   Color descriptors
   function F2=colorFXhist6(x)
   H=x(:,:,1); S=x(:,:,2); V=x(:,:,3);
   [m,n]=size(H); N=m*n;
   t1=1/18; t2=1/9; t3=75/360; t4=155/360; t5=190/360; t6=270/360; t7=295/360;
   t8=315/360;
   for i=1:m
    for j=1:n
        if H(i,j)<=t1 | H(i,j)>t8
            h_bin=0;
        elseif t1<H(i,j) & H(i,j)<=t2
            h_bin=1;
        elseif t2<H(i,j) & H(i,j)<=t3
            h_bin=2;
        elseif t3<H(i,j) & H(i,j)<=t4
            h_bin=3;
        elseif t4<H(i,j) & H(i,j)<=t5
            h_bin=4;
        elseif t5<H(i,j) & H(i,j)<=t6
            h_bin=5;
        elseif t6<H(i,j) & H(i,j)<=t7
            h_bin=6;
        elseif t7<H(i,j) & H(i,j)<=t8
            h_bin=7;
        end      
        

Color descriptors
Color Histogram Usage
-	Color histograms provide a concise but coarse characterization of an image
-	Often used in CBIR (content based image retrieval) systems
-	Large database of images which can be searched by image content, not by keyword
-	Use color histograms to refine the search
-	Similar histograms are likely to reflect visually similar source images
-	Two very dissimilar source images may have two similar histograms
Summary
We have looked at:
-	Boundary descriptor
-	Region descriptor
-	Relational descriptor

## Object-Recognition
Luguangm@gmail.com
The Basic Idea
Object Recognition: Classification & Clustering

Objects characterized by one or more features

Classification
-	Have labels for some points
-	Want a “rule” that will accurately assign labels to new points
-	Supervised learning

Clustering
-	No labels
-	Group points into clusters based on how “near” they are to one another
-	Identify structure in data
-	Unsupervised learning
Features & Classifier
-	Features: can be regarded as the descriptors we introduced before.
-	Representation useful for classification:
-	Easy to extract (reliable)
-	Strong edges, texture, color, etc…
-	Specific for the classes
-	Green pears vs. red tomatoes

-	Classifier: Algorithm/device that separates objects into different classes.


Features
Feature Space
-	The set of all possible patterns forms the Feature vector.
-	Each feature vector is a point in the so-called feature space.
-	Similar objects yields similar measurement results (feature vectors). Nearby points in feature space correspond to similar objects.
-	Distance in feature space is related to similarity.
Pattern Classification
Recognition Based on Decision-Theoretic Method
Recognition Based on Decision-Theoretic Method
Pattern Classification
-	Given a decision boundary, we can compute the classification error.
-	The decision boundary that have minimum classification error is the optimal decision boundary.


Pattern Classification
-	The number of classes is usually known beforehand, and typically can be derived from the problem specification.
-	The classifier does not decide about the class from the object itself — Depend on object properties called patterns (Features).
-	The classes (clouds) may have wide variety of shapes.
-	Decision functions specify the boundaries between the clouds. These boundaries can be used for classification of new, unidentified objects.
-	If two clouds that represent different classes overlap in feature space, then a perfect classification does not exist.
-	Multiple classes require multiple decision functions.

Minimum Distance Classifier
Minimum Distance Classifier
Minimum Distance Classifier
Minimum Distance Classifier
More Dimensional Features
-	Problems:
-	Not all of features are discriminating!
-	Using more features requires exponentially more data.
-	Feature selection: Select a subset of features that are most discriminative.
Feature Selection
-	Features which show less overlap between classes are more discriminative.
-	A simple measure for class separability is:

Performance of Classifier
Pattern Classification
-	应用实例：智能电子秤
Pattern Classification
-	系统精度
Pattern Classification
-	特点与优势
Cluster Analysis
Data clustering is a common technique for statistical data analysis, which is used in many fields, including machine learning, data mining, pattern recognition, and image analysis.

Cluster Analysis methods do not need training sets for learning

Cluster Analysis methods divide the set of processed patterns into subsets (clusters) based on the mutual similarity of subset elements.
-	Each cluster contains patterns representing objects that are similar according to the selected object description and similarity criteria.
-	Objects that are not similar reside in different cluster.

Distance Measure
-	An important step in any clustering is to select a distance measure, which determines how the similarity of two elements is calculated. 
-	This will influence the shape of the clusters, as some elements may be close to one another according to one distance and further away according to another.
-	E.g., Euclidean distance, Manhattan distance…
K-means
The k-means cluster analysis method is a well known example of cluster analysis.

-	Popular
-	Simple
-	And practically useful

K-means
-	Choose the number of clusters, k. 
-	Randomly generate k cluster starting points in n-dimensional feature space.
-	Assign each point to the nearest cluster center. 
-	Recompute the new cluster centers. 
-	Jump to the new center.
-	Repeat the previous steps (3-5) until some convergence criterion is met (usually that the assignment hasn't changed). 





Discussion
-	The main advantages of this algorithm are its simplicity and speed which allows it to run on large datasets. 

-	Its disadvantage is that it does not yield the same result with each run, since the resulting clusters depend on the initial random assignments. 

-	It minimizes intra-cluster variance, but does not ensure that the result has a global minimum of variance. 
An Application: Biometrics


Biometrics: What you are/do!



Challenges of Biometrics



Challenges of Biometrics
Challenges of Biometrics


Biometric: Palmprint
Why Palmprint:
-	Convenient & comfortable
-	Not easy to get hurt
-	Stable for a long period of time
-	High accuracy
-	Palmprint tells more than fingerprint
-	Complementary to other biometric systems
Plamprint Acquisition



Palmprint Preprocessing
Line Feature Extraction Method
Line Feature Matching Method
Matching Result
Texture based Feature Extraction
Gabor Filter


Adjusted Gabor Filter to zero DC to provide more robust to brightness. 




The parameters, such as the sizes and orientation, are critical to provide good experiment results.
Texture based Feature Extraction
Texture based Feature Extraction
Each feature vector is two 2D matrices: real and imaginary part
Texture based Feature Extraction
Texture based Feature Extraction
Texture based Feature Extraction


Biocomputing Research Center

## Image Compression

Contents
Today we will begin looking at image Compression:
-	Fundamentals
-	Data Redundancies
-	Image Compression Models
-	Lossless Image Compression
Fundamentals
Motivation
-	Much of the on-line information is graphical or pictorial, storage and communications requirements are immense.
-	The spatial resolutions of today’s imaging sensors and the standard of broadcast television are greatly developed.
-	1991, Kodak, MC-200, 0.4M pixels, 1.7M Japan Yuan(around 95000RMB). 
-	June 2020, Canon IXUS 190, 20M pixels, around 950RMB(JD).
-	VGA resolution: 320×240, 640×480(标清), 1280×720(高清), 1920×1080(1080P), 3840x2160(4K), 7680×4320(8K)…
-	Methods of compressing the data prior to storage and/or transmission are of practical and commercial interest.
Fundamentals
-	Image compression addresses the problem of reducing the amount of data required to represent a digital image.
-	Remove the redundant data.
-	Statistically uncorrelated data set.
-	The transformation is applied prior to storage or transmission of the image.
-	Decompressed to reconstruct the original image or an approximation of it.

Fundamentals
Applications
-	Increased spatial resolutions
-	Image sensors
-	Broadcast television standards
-	Tele-video-conferencing
-	Remote sensing
-	Document and medical imaging, fax
-	An ever-expanding number of applications depend on the efficient processing, storage, and transmission of binary, gray-scale, and color images
Classification 
Lossless compression
-	Also called information preserving compression or error-free compression.
-	Lossless compression for legal and medical documents, remote sensing. 
Lossy compression
-	Provide higher levels of data reduction.
-	Useful in broadcast television, video conference, internet image transmission.
-	Where some errors or loss can be tolerated.
Fundamentals
Data and Information
-	Distinguish the meaning of data and information:
-	Data: the means by which information is delivered. 
-	Information: various amounts of data may be used to present the same amount of information.
-	Data Compression:
-	The process of reducing the amount of data required to represent a given quantity of information.
Fundamentals
Example: Story
- Story is information
- Word is data
- Data Redundancy
-	If the two individuals use a different number of words to tell the same basic story.
-	At least one includes nonessential data. It is thus said to contain data redundancy.
-	Eg. 100000000 & 108, People‘s Republic of China: PRC.

Fundamentals
Data redundancy is the central issue in digital image compression.


Data Redundancy
Data Redundancy: it is not an abstract concept, but a mathematically quantifiable entity.
-	If n1 and n2 denote the number of information-carrying units in two data sets that represent the same information.
-	The relative data redundancy RD of the first data set can be defined as:
				RD = 1 −1/CR
			where compression ratio CR is
				CR =n1/n2
			Data Redundancy
			CR and RD: RD = 1 −1/CR
-	When n2=n1, CR=1 and RD=0
-	When 
-	When                                              , not hoped situation.
-	Usually, CR and RD lie in the open intervals           and          .
-	Example: 
-	Compression ratio is 10:1.
-	Redundancy is 0.9.
-	It implies that 90% of the data in the first data set is redundant.
Data Redundancy
In image processing, there are three basic data redundancies can be identified and exploited:
-	Coding Redundancy
-	Interpixel Redundancy
-	Psychovisual Redundancy

Data compression is achieved when one or more of these redundancies are reduced or eliminated.
Coding Redundancy
-	A discrete random variable rk in the interval [0,1], represents the gray levels.
-	Each rk occurs with probability pr(rk):



-	If the number of bits used to represent each value of rk is l(k), then the average number of bits required to represent the image pixel is:



-	The total number of bits required to code an M×N image is MNLavg.
Coding Redundancy
-	For example, the gray levels of an image with a natural m-bit binary code. 
-	The constant m may be taken outside the summation, leaving only the sum of the pr(k) equals 1.
-	Then: Lavg=m


Coding Redundancy
Coding Redundancy
-	For code 2, the average number of bits required to code the image is reduced to:






-	The resulting compression ratio CR is 3/2.7 or 1.11.
-	Redundancy is RD = 1−1/1.11 = 0.099.
Coding Redundancy
Coding Redundancy
Interpixel Redundancy
Interpixel Redundancy
Interpixel Redundancy
Interpixel Redundancy
Interpixel Redundancy
Interpixel Redundancy
Interpixel Redundancy
Psychovisual Redundancy
Psychovisual Redundancy
Basic Cognitive Procedure
-	Human perception of the information in an image normally does not involve quantitative analysis of every pixel value in the image.
-	Find features such as edges or textual regions.
-	Mentally combines them into recognizable groupings.
-	The brain correlates these groupings with prior knowledge.
-	Then, complete the image interpretation process.
Psychovisual Redundancy
-	Lead to a loss of quantitative information (quantization).
-	Mapping a broad range of input values to a limited number of output values.
-	Irreversible Operation.
Psychovisual Redundancy
Psychovisual Redundancy
Psychovisual Redundancy
Psychovisual Redundancy
Improved Gray-Scale (IGS) quantization
-	A sum: initially set to zero.
-	Add the four least significant bits of a previously generated sum with current 8-bit gray level.
-	If the four most significant bits of the current value are 11112, however, 00002 is added instead.
-	The four most significant bits of the resulting sum are used as the coded pixel value. 

Fidelity Criteria
-	Compression may lead to loss information
-	Quantifying the nature and extent of information loss
-	Objective fidelity criteria
-	When the level of information loss can be expressed as a function of the input image, the compressed and output image.
-	Easy to operate (automatic).
-	Often requires the original copy as the reference.
-	Subjective fidelity criteria
-	Evaluated by human observers. 
-	Do not require the original copy as a reference.
-	Most decompressed images ultimately are view by human.
Fidelity Criteria
Objective Fidelity Criterion
Root-Mean-Square (rms) Error:
-	Let f(x, y) represent an input image
-	Let             be an estimate or approximation of f(x, y)
-	For any value of x and y, the error e(x, y) is given by:

-	The root-mean-square error (erms) is
Fidelity Criteria
Mean-Square Signal-to-Noise Ratio:
-	Assuming that            is the sum of the original image f(x, y) and a noise signal e(x, y).
-	The mean-square signal-to-noise ratios of the output image (SNRms) is:



-	SNRrms is the square root of above equation.
Fidelity Criteria
Subjective fidelity Criterion
-	Most decompressed images are viewed by humans.
-	Measuring image quality by the subjective evaluations is more appropriate.
-	Example: evaluation or voting.
Fidelity Criteria
Fidelity Criteria
Compression Models
-	Three general techniques are always combined to form practical image compression systems.
-	The overall characteristics of compression:
-	A compression system consists of two distinct structural blocks: an encoder and a decoder.
-	Encoder and a decoder: Source and Channel.
-	Channel encoder: Increases the noise immunity of the source encoder’s output. If noise free, can be omitted.


Compression Models
The Source Encoder and Decoder
-	Source Encoder: Remove input redundancies.
-	Each operation is designed to reduce or eliminating one of the three redundancies. 
-	Interpixel redundancy (Mapper, reversible).
-	Psychovisual redundancy (Quantizer, irreversible). 
-	Coding redundancy (Symbol Encoder, reversible).
Compression Models
Three steps for source encoder:
-	First, the mapper transforms the input data into a format designed to reduce interpixel redundancies in the input image (run-length coding), this operation generally is reversible.
-	Second, quantizer block reduces the accuracy of the mapper’s output in accordance with some pre-established fidelity criterion. This stage reduces the psychovisual redundancies of the input image. It is irreversible.
-	Third, symbol coder creates a fixed or variable length code to represent the quantizer output. It can reduce coding redundancy, and it is reversible.
Compression Models
Remark
-	The quantizer must be omitted when error-free compression is desired.
-	Some compression techniques normally are modeled by merging blocks that are physically separate in above figure.
-	The source decoder only contains two blocks: symbol decoder and an inverse mapper. Because quantization results in irreversible information loss, an inverse quantizer block is not included in the general source decoder model.
Compression Models
The Channel Encoder and Decoder
-	They play an important role in the overall encoding-decoding process when the channel is noisy or prone to error.
-	Reduce the impact of channel noise
-	Insert a controlled form of redundancy into the source encoded data.
-	Source encoder contains little redundancy 
-	It would be highly sensitive to transmission noise without the addition of this "controlled redundancy".
Lossless Image Compression
-	Also called Error-Free compression
-	The need for error-free compression is motivated by the intended use or nature of the images. 
-	In some applications, it is the only acceptable means of data reduction. 
-	Archival of medical or business documents, where lossy compression usually is prohibited for legal reasons.
-	Other is the processing of satellite imagery, where both the use and cost of collecting the data makes any loss undesirable.
-	Another is digital radiography, where the loss of information can compromise diagnostic accuracy.
Lossless Image Compression
-	Two relatively independent operations
-	Eliminate coding redundancies.
-	Reduce interpixel redundancies.
-	They normally provide compression ratios of 2 to 10.
-	Approaches:
-	Variable length coding(coding redundancies)
-	Huffman coding
-	Arithmetic coding
-	LZW coding(interpixel redundancies)
Variable-Length Coding
-	Reducing coding redundancy: assign the shortest possible code words to the most probable gray levels.
-	Huffman Coding
-	Arithmetic Coding
-	Remark: The source symbols may be either the gray levels of an image or the output of a gray-level mapping operation.

Huffman Coding
Huffman coding, 1952
Coding Procedures for an N-symbol source, two steps:
-	Source reduction
-	List all probabilities in a descending order.
-	Merge the two symbols with smallest probabilities into a new compound symbol.
-	Repeat the above two steps until a reduced source with two symbols.
-	Codeword assignment
-	Start from the smallest source and work back to the original source.
-	Each merging point corresponds to a node in binary codeword tree.
Example-I





Example-III

## Image Compression2

Contents
Image Compression:
-	Lossless Image Compression
-	Variable length coding(Eliminate coding redundancies)
-	Huffman coding
-	Arithmetic coding
-	LZW coding (Reduce interpixel redundancies)
-	Hybrid Coding


-	Lossy Image Compression
Arithmetic coding
-	Elias (1963) thought that: a one-to-one correspondence between source symbols and code words does not exist.
-	An entire sequence of source symbols is assigned a single arithmetic code word.
-	The code word defines an interval of real numbers between 0 and 1.
-	As the number of symbols in the message increases, the interval used to represent it becomes smaller. 
-	And the number of information units (bits) required to represent the interval becomes larger.









LZW Coding
-	Invented by Lempel and Ziv in 1977, and improved by Welch in 1984 (interpixel redundancies).
-	Numerous variations and improvements since then.
-	United States Patent No. 4,558,302, LZW compression has been integrated into a variety of mainstream imaging files formats:
-	Including the graphic interchange format (GIF)
-	Tagged image file format (TIFF)
-	And the portable document format (PDF)
LZW Coding
-	Lempel-Ziv-Welch (LZW) coding
-	Assigns fixed-length code words to variable length sequences of source symbols.
-	Requires no priori knowledge of the probability of occurrence of the symbols to be encoded.
-	LZW coding is conceptually very simple.
-	At the onset of the coding process, a codebook or ’dictionary’ containing the source symbols to be coded is constructed.
LZW Coding
Example:8-bit monochrome images
-	The first 256 words of the dictionary are assigned to the gray values 0, 1, 2, · · · , 255.
-	As the encoder sequentially examines the image’s pixels, gray-level sequences that are not in the dictionary are placed in algorithmically determined locations.
-	If the first two pixels of image are white, for instance, sequence 255-255 might be assigned to location 256, the address following the locations reserved for gray levels 0~255.
-	The next time, when 255-255 is encountered, code word 256, the address of the location containing this sequence, is used to represent them.
LZW Coding
-	If a 9-bits, 512 word dictionary is employed in the coding process, the original (8+8) bits that were used to represent the two pixels are replaced by a single 9-bit code word.
-	The size of the dictionary is an important system parameter.
-	If it is too small, the detection of matching gray-level sequences will be impossible.
-	If it is too large, the size of the code words will adversely affect compression performance.
LZW Example
Example: Consider the following 4 × 4, 8-bit image of a vertical edge:


LZW Example
LZW Example
LZW Example
LZW Example
Decoding
Define two variables:
-	S1: to store the input data (encoded data) one by one.
-	S2: a temporary variable.
-	They are initialized to empty.



LZW coding
Hybrid Coding
Hybrid Coding
Lossy Image Compression
-	Lossy image compression
-	Why lossy?
-	An simple example
-	Lossy transform coding
-	Image Transform Selection
-	Wavelet Coding
-	Joint Photographic Expert Group (JPEG) 
Why Lossy?
In most applications related to consumer electronics, lossless compression is not necessary.
-	What we care is the subjective quality of the decoded image, not those intensity values.
-	Compromising the accuracy of the reconstructed image in exchange for increased compression.
-	If the resulting distortion can be tolerated, the increase in compression can be effective.
Why Lossy?

-	With the relaxation, it is possible to achieve a higher compression ratio (CR).
-	Many lossy encoding techniques are capable of reproducing:
-	Recognizable monochrome images from data that have been compressed by more than 100:1.
-	And images that are virtually indistinguishable from the originals at 10:1 to 50:1.
-	Lossless coding of monochrome images, however, seldom results in more than a 3:1 reduction in data.


Transform Coding
For the purpose of image compression, the transform operation should be:
-	To decorrelate  (“Break”) the correlations. Or to pack as much information as possible into the smallest number of transform coefficients.
-	Basis transform functions are independent on the input images.
-	Fast computation.
-	Typical transforms:
-	DFT transform
-	DCT transform
-	Walsh Hadamard Transform
-	KL transform

DFT
Discrete Fourier Transform (DFT)
-	Use cos+sin as its basis functions.
-	Fast Fourier Transform (FFT).
-	Not so popular in image compression because.
-	Performance is not good enough.
DFT
Discrete Fourier Transform (DFT)

DCT
Discrete Cosine Transform (DCT)
-	Use cosine function as its basis function
-	Performance approaches KLT
-	Fast algorithm exists
-	Most popular in image compression application
-	Adopted in JPEG
2D DCT
 2D DCT: transfer spatial domain to frequency domain
-	 DC: F(0,0), average of f(x,y)
-	 AC: other coefficients
WHT
Walsh-Hadamard Transform (WHT)
-	Simple basis functions: all kernel values are +1 or -1.
-	Only addition and subtraction operations are needed.
-	Popular when efficiency consideration dominates.
-	Performance moderate.
WHT
Transform Selection
Transform Selection
KL Transform
Karhunen-Loeve Transform (KLT)
-	Optimal transform, better than prior transforms.
-	Basis functions are image dependent.
-	No fast algorithm exists.
-	Not so useful in image compression.
-	Usually used for comparison.
Transform Selection
Transform Selection
Wavelet Transform
Wavelet Transform
-	The main difference between the wavelet transform and the transform coding system is the omission of the transform coder’s subimage processing stages.
-	Because wavelet transfroms are both computationally efficient and inherently local, subdivision of the original image in unnecessary.
Wavelet Transform
-	Initially, wavelet transform just focused on the 1-D situation. 
-	2-D wavelets, in the same way, can be treated as two 1-D wavelet transforms: one 1-D wavelet transforms along the row direction and the other one along the column direction. 
-	Generally, an one-octave transform of an image can be decomposed into four components: low-pass rows, low-pass columns (LL); low-pass rows, high-pass columns (LH); high-pass rows, low-pass columns (HL); high-pass rows, high-pass columns (HH). 
Wavelet Transform
Wavelet Transform
Wavelet Transform
JPEG
-	JPEG - Joint Photographic Experts Group (联合图像专家组)
-	Joint refers to CCITT (国际电报电话咨询委员会) and ISO (国际标准化协会).
-	Compression standard of general continuous-tone still image.
-	Became an international standard in 1992.
-	It defines three different coding systems
-	A lossy baseline coding system, which is based on the DCT and is adequate for most compression applications.
-	An extended coding system for greater compression, higher precision, or progressive reconstruction applications.
-	A lossless independent coding system for reversible compression.
JPEG 2000
Eliminates the blocky appearance of the JPEG image standard.
-	This is because it uses a wavelet transform instead of the discrete cosine transform (DCT).
-	In the previous DCT version, blocks of the image are compressed individually without reference to the adjoining blocks.
-	Using a DWT creates a much smoother image.
The compression rate is much higher, while the retention rate is the same, and often, better resolution is exhibited.
-	20%-200% better than JPEG Standard with lossy compression.
JPEG vs. JPEG 2000
Thanks and Good Luck!

