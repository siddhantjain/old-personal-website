---
layout: post
title: Monitoring an AWS cloud using the AWS SDK
date: 2015-03-30 09:14
author: siddhantj
comments: true
categories: [AWS, Cloud, Cloud Monitoring, Computer Science, java]
---
I have now started working on a project that requires me to monitor some metrics from AWS. At first i attempted to use use the Query API provided by AWS and get the desired information by just making some REST calls, however I haven't been able to make it run correctly yet. So now I am resorting to using the SDK which is supposed to make the process easier. (I will get back to the REST API in a while).

Writing this tutorial to archive the steps needed to use the AWS SDK for monitoring clouds using Java.
I will be using eclipse for this purpose.

<strong>Setting up Eclipse</strong>

You will first require to install the Java SDK in eclipse. Follow the steps given here. It is possible you might run into an error which says:
<blockquote>Cannot complete the install because one or more required items could not be found. Software currently installed: Amazon SimpleDB Management 1.0.0.v201111161400 (com.amazonaws.eclipse.datatools.enablement.simpledb.feature.feature.group 1.0.0.v201111161400) Missing requirement: Eclipse Data Tools Platform Amazon SimpleDB UI Plug-in 1.0.0.v201111161400 (com.amazonaws.eclipse.datatools.enablement.simpledb.ui 1.0.0.v201111161400) requires 'bundle org.eclipse.datatools.sqltools.sqlscrapbook 1.0.0' but it could not be found Cannot satisfy dependency: From: Amazon SimpleDB Management 1.0.0.v201111161400 (com.amazonaws.eclipse.datatools.enablement.simpledb.feature.feature.group 1.0.0.v201111161400) To: com.amazonaws.eclipse.datatools.enablement.simpledb.ui [1.0.0.v201111161400]

&nbsp;</blockquote>
To resolve install both the SDKs under data development when you select "All available sites" in the drop down menu  at "Work With". This is some bug with the AWS SDK. They are supposed to install all the dependencies themselves but that evidently doesn't happen.

&nbsp;
<blockquote>A short note on incorrect html formatting

So, in your attempts to follow tutorials on the internet you might come across code which might have incomprehensible strings like

<span class="typ">HashMap</span><span class="pun">&amp;</span><span class="pln">lt</span><span class="pun">;</span><span class="typ">String</span><span class="pun">,</span> <span class="typ">String</span><span class="pun">&amp;</span><span class="pln">gt</span><span class="pun">;</span>

This is just poor HTML formatting. &amp;lt; stands for &lt; and &amp;gt; stands for &gt;. Suddenly, everything will make much more sense.</blockquote>
Anyway, so on wards with our main goal of writing a program which can get some metrics from AWS.

<strong>Using the SDK: Code and other paraphernalia </strong>

Coming to the code part. How exactly a cloud monitor's code is to be written will totally depend on why do you want to monitor the cloud. That means, you will have to decide what metrics to poll, how frequently should you get those metrics etc. For the purpose of illustration a code i found on a stackoverflow is extremely useful. This code collects CPU Utilisation from a running EC2 instance. I am adding that code here:
<blockquote>&nbsp;

import java.util.Date;

import com.amazonaws.auth.BasicAWSCredentials;
import com.amazonaws.services.cloudwatch.AmazonCloudWatchClient;
import com.amazonaws.services.cloudwatch.model.Datapoint;
import com.amazonaws.services.cloudwatch.model.Dimension;
import com.amazonaws.services.cloudwatch.model.GetMetricStatisticsRequest;
import com.amazonaws.services.cloudwatch.model.GetMetricStatisticsResult;

public class AmazonCloudWatchFetchCpuUtilTest {

public static void main(String[] args) {
final String awsAccessKey = "Enter your value";
final String awsSecretKey = "Enter your value";
final String instanceId = "Enter your value";

final AmazonCloudWatchClient client = client(awsAccessKey, awsSecretKey);
final GetMetricStatisticsRequest request = request(instanceId);
final GetMetricStatisticsResult result = result(client, request);
toStdOut(result, instanceId);
}

private static AmazonCloudWatchClient client(final String awsAccessKey, final String awsSecretKey) {
final AmazonCloudWatchClient client = new AmazonCloudWatchClient(new BasicAWSCredentials(awsAccessKey, awsSecretKey));
client.setEndpoint("monitoring.us-east-1.amazonaws.com");
return client;
}

private static GetMetricStatisticsRequest request(final String instanceId) {
final long twentyFourHrs = 1000 * 60 * 60 * 24;
final int oneHour = 60 * 60;
return new GetMetricStatisticsRequest()
.withStartTime(new Date(new Date().getTime()- twentyFourHrs))
.withNamespace("AWS/EC2")
.withPeriod(oneHour)
.withDimensions(new Dimension().withName("InstanceId").withValue(instanceId))
.withMetricName("CPUUtilization")
.withStatistics("Average", "Maximum")
.withEndTime(new Date());
}

private static GetMetricStatisticsResult result(
final AmazonCloudWatchClient client, final GetMetricStatisticsRequest request) {
return client.getMetricStatistics(request);
}

private static void toStdOut(final GetMetricStatisticsResult result, final String instanceId) {
System.out.println(result);
for (final Datapoint dataPoint : result.getDatapoints()) {
System.out.printf("%s instance's average CPU utilization : %s%n", instanceId, dataPoint.getAverage());
System.out.printf("%s instance's max CPU utilization : %s%n", instanceId, dataPoint.getMaximum());
}
}
}</blockquote>
&nbsp;

Try running the code on your machine, there is a chance you might run into an error which says something like this: <a href="http://stackoverflow.com/questions/4062307/pkix-path-building-failed-unable-to-find-valid-certification-path-to-requested">PKIX path building failed: unable to find valid certification path to requested target</a>

&nbsp;

I found a fix for this error on this website: <a href="http://myshittycode.com/2014/06/05/java-https-unable-to-find-valid-certification-path-to-requested-target/">http://myshittycode.com/2014/06/05/java-https-unable-to-find-valid-certification-path-to-requested-target/</a>

You basically have to generate a certificate file for the end point which you are using for monitoring the cloud.

I guess that's all you will need to get started. Using this as a guide, you can go on to make your full fledged cloud monitor!
