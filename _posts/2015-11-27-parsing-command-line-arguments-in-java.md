---
layout: post
title: Parsing command line arguments in Java
date: 2015-11-27 09:45
author: siddhantj
comments: true
category: tech
---
When you have command line arguments, the beginners instinct is to resort to the basics and "parse" them by using the "args" parameter and indexing into it (like: args[0] , args[1] etc.) . Having succumbed to this instinct recently, i learnt the hard way to write better code always. Thought I must archive.

One of the problems with such basic parsing is the presences of spaces. if you command line arguments have spaces in them, you will end up having messing up the args because space is considered the separator between arguments.

A better approach to use a parser which will look for options and the corresponding values. This is similar to options you are used to giving with almost every thing that you invoke from the command line.  for example:

java -jar abc.jar

&nbsp;

here "jar" is the option that I am talking about.

&nbsp;

Anyway, Java has a neat little library to do all of this. I would have written some code to illustrate how to use, but I think the official documentation of the library does a good job, so I will leave it to that.

Check out: <a href="https://commons.apache.org/proper/commons-cli/">https://commons.apache.org/proper/commons-cli/</a>

This  will do everything you want and make you feel like you are writing good code.

Will update this post with some code where I have used this library, sometime in future, hopefully.

&nbsp;
