---
layout: post
title: Kaggle - Data Science London + Scikit-learn (Using SVM in R)
date: 2013-12-31 17:22
author: siddhantj
comments: true
categories: [Machine Learning, MATLAB R Kaggle]
---
I finished a course on machine learning last semester and came across kaggle some time around then. I immediately pushed it in my winter-break-to-do list and lucky kaggle got selected as the only thing that actually happened from that list.

Anyhow, I have been working on the easiest <a href="http://http://www.kaggle.com/c/data-science-london-scikit-learn" target="_blank">problem</a> they have on kaggle and my performance has been abysmal, at best.

I wanted to get a result out there right away so implemented a model using what I knew already - MATLAB. Having done an assignment for the aforementioned course, I knew how to implement SVM in MATLAB and submitted an answer based on ten minutes of work. I was placed in the bottom twenty of 150+ teams (accuracy of ~80%). Like I said, abysmal.

A little reading on the forums and quora suggested that MATLAB might not be a logical choice for solving problems on Kaggle and hence I shifted to R thereafter. The implementation of SVM in R (in the e0171 package) improved my accuracy by roughly 3%.

The dataset has some 40 attributes and so, it looks like some dimensionality reduction technique should improve the results. However, I want to try different basic implementations first. I am trying the k nearest neighbours approach next.

I am appending the R commands for implementing SVM on the given data set, for future reference
<blockquote>setwd("C:/Users/Siddhant/Kaggle/Data Science london + Scikit-learn")

#reading the datasets. The first row in the csv files should be the titles for the columns

test &lt;- read.csv("test 1.csv")

train &lt;- read.csv("train1.csv")

trainLabels &lt;- read.csv("trainLabels1.csv")

#building the model. Since the trainLabels is not of type factor, we need to explicitly mention the type to be C-Classification to avoid regression

model &lt;- model &lt;- svm(train,trainLabels, type = 'C-classification')

pred &lt;- predict(model,test)

write.csv(pred,"answer.csv")</blockquote>
