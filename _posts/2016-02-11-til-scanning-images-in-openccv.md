---
layout: post
title: TIL - Scanning Images in openCV
date: 2016-02-11 12:48
author: siddhantj
comments: true
category: tech
---

Often, we would want to scan through each and every pixel of an image in openCV. Now, if you are new to openCV (or lazy about writing code), in all probability, you will end up using the the inbuilt `at` function to access the pixels. This choice is understandable because all you need to do to specify the co-oridnates of the pixel and you get access to the pixel. However, this method is painfully slow and if you need to optimise your OpenCV code, this should probably the first place you should look at. If you are going to access each and every pixel in the image, in sequence, instead of relying on the `at` function (which was written to provide ease in random access of pixels in an image), you should go for the pointer based access. That means, get the pointer to the starting location of the image data and increment the pointer by an amount equivalent to the size of one pixel to access the next pixel. Let me explain with some code. Assume we have an image stored in Mat called imageMat. This image is an RGB image with each channel (R,G or B) of size 8 bits (1 Byte). This mean, each pixel is of size 3 bytes. In the following code, we will declare a uchar pointer (pointer pointing to a memory of 1 Byte) and increment it by 3, everytime we want to access the next pixel (simple pointer arithmetic at play here). 

{% highlight cpp %}
{%raw%}
int numOfRows = imageMat.rows;
int numOfCols = imageMat.cols;
int numOfPixels = numOfRows*numOfCols;

uchar* pImage = imageMat.data;
Vec3b pixelColour;
for(int i=0;i<numOfPixels;i++,pImage+=3)
{
	pixelColour[0] = pImage[0];
	pixelColour[1] = pImage[1];
	pixelColour[2] = pImage[2];
	
}

{%endraw%}
{% endhighlight %}

Another interesting thing to do to speed up your image processing routines (or a any routine if it is applicable) is to use look up tables. The idea is simple, if you have to perform the same arithmetic on each channel of each pixel in the picture, the most intuitive solution that might come to your mind will be to simply go to each pixel and perform the arithmetic. However, you will be doing a lot of repeated calculations here, as you might have already calculated these values previously. since we know that a pixel channel value might vary just from 0-255, we simply calculate all possible values for all possible colour combinations and store it. Next time we encounter a channel in a pixel with some value, we simply look-up in the table we created, instead of performing a calculation on the pixel. An example will make it clearler. Suppose in the above table, you want to calculate a weighted sum of the channel values with weights `a` and `b` and store it another variable.

One way to go about this will be: 
{% highlight cpp %}
{%raw%}
int numOfRows = imageMat.rows;
int numOfCols = imageMat.cols;
int numOfPixels = numOfRows*numOfCols;
float a = 0.5;
float b = 0.7;
float calculatedValue;
uchar* pImage = imageMat.data;
Vec3b pixelColour;
for(int i=0;i<numOfPixels;i++,pImage+=3)
{
	pixelColour[0] = pImage[0];
	pixelColour[1] = pImage[1];
	pixelColour[2] = pImage[2];
	calculatedValue = pixelColour[0] + a*pixelColour[1] + b*pixelColour[2];
}
{%endraw%}
{% endhighlight %}

However another possible (faster) way could be this:

{% highlight cpp %}
{%raw%}
int calculatedValueArray[256][256]256];
for(int i=0;i<256;i++)
	for (int j=0;j<256;j++)
	for(int k=0;k<256;k++)
	{
		calculatedValueArray[i][j][k] = i + a*j + b*k;
	}

int numOfRows = imageMat.rows;
int numOfCols = imageMat.cols;
int numOfPixels = numOfRows*numOfCols;
float a = 0.5;
float b = 0.7;
float calculatedValue;
uchar* pImage = imageMat.data;
Vec3b pixelColour;
for(int i=0;i<numOfPixels;i++,pImage+=3)
{
	pixelColour[0] = pImage[0];
	pixelColour[1] = pImage[1];
	pixelColour[2] = pImage[2];
	calculatedValue = calculatedValueArray[pixelColour[0]][pixelColour[1]][pixelColour[2]];
}
{%endraw%}
{% endhighlight %}  

Given you are dealing with high-res images with millions of pixels, the second method will prove itself than the first in such cases.