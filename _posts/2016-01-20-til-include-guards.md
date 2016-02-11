---
layout: post
title: TIL - Include guards
date: 2016-01-20 08:57
author: siddhantj
comments: true
category: tech
---
Small post to note down a small thing about programming that I learnt today because of some error I was getting.

I will speak for C++ here.

So, basically, you would have divided your code in header files (.h) and the source files (.cpp). Now to refer to these files you will do an #include "somefile.h"

The error you might potentially run into is that of duplicate declarations. How?

Like So:
{% highlight cpp %}
{%raw%}
File1.h

int A;

File2.h
#include "File1.h"
int B;

File3.cpp
#include"File1.h"
#include"File2.h"

/..some code../
{%endraw%}
{% endhighlight %}
Now when we do the expansion of the #include, it is clear to see that we will have multiple declarations of the variable A which will lead to a compilation error.

Include guards are a good practice followed while programming to avoid such scenarios. Basically all you do is that ensure if a header file is already present in the file, it need not be expanded again.

So, I will change my File1.h to add include guards like follows:

{% highlight cpp %}
{%raw%}
#ifndef __FILE_1_H
#define __FILE_1_H

int A;

#endif
{%endraw%}
{% endhighlight %}
Now, as will be rightly noted, a second expansion of File1.h will not allow for a duplicate declaration of variable A as #ifndef will evaluate as false.

[This](http://stackoverflow.com/a/8020211/1910621) SO answer is better than my explanation, btw:

