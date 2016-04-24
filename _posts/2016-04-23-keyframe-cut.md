---
title: "Key Frame Cut"
layout: post
date: 2014-07-02 22:10
tag: jekyll
img: indigo/indigo.png
projects: true
description: "A novel algorithm to perform real-time background separation of talking head videos."
jemoji: '<img class="emoji" title=":scissors:" alt=":ramen:" src="https://assets.github.com/images/icons/emoji/unicode/2702.png" height="20" width="20" align="absmiddle">'
---

Keyframe cut is a background removal algorithm for videos that was developed as a part of a summer internship project with the eLearning team at Adobe Systems, Bangalore. We started our project with a comprehensive iterature survey - populating a knowledge base on computer vision for internal use. Thereafter we focused on devising novel algorithms to solve the problem, given our constraints with talking head videos. Keyframe cut fuses multiple cues to segment each frame of the video. Starting from a completely segmented frame given by the user - the keyframe, we employed GMMs (colour cues), face/body detectors (shape cues) and frame differences (motion cues) to segment each frame. Our final algorithm had an average error rate of under 6% for a test dataset.

The algorithm was developed in C++, using openCV primarily for most Computer Vision based algorithms. Bauilding on the work done during this project, a feature - "make my background awesome" was incorporated as a marquee feature in the the product Adobe Presenter Video Express 11.0.