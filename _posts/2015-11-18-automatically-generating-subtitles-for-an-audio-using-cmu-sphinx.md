---
layout: post
title: Automatically Generating Subtitles for an Audio using CMU Sphinx
date: 2015-11-18 10:25
author: siddhantj
comments: true
category: tech
---
A small tutorial to generate subtitles for an audio automatically using CMU Sphinx.

CMU Sphinx is probably the most popular open source project for Automatic Speech Recognition and understandably so. Their Java APIs are extremely simple to use and play with. All you need is to add the Jar files and you are sorted.

For those who are just starting out with Sphinx (just like I was a week ago probably), I have written a small program that takes an Audio file as input and returns the corresponding subtitles for the Audio file. This effectively serves as an example to do transcriptiong of continuous speech with large vocabulary with Sphinx.

I am sharing my entire project (along with the dataset) on <a href="https://github.com/siddhantjain/SphinxTest">Github</a>. Note that the the files in the dataset have been converted into the specifications (16 Khz, Mono...etc.) required by Sphinx. I used FFMPEG for that purpose.

&nbsp;

Please feel free to contribute / open issues on the project.
