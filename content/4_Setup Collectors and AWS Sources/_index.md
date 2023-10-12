---
title: Set Up Collectors and AWS Sources
chapter: true
weight: 11
---

## Set Up Collectors and AWS Sources - 1

Use this module to set up collectors to send data to Splunk etc



1. Name the _Collector_ (e.g. vpcflowlogs )
2. Name the _S3 bucket_ (If the name of the bucket contains dashes (-), plase single quotes (‘’)around the name to avoid error messages.)
3. Select the _Region_ of the S3 bucket
4. Set the _Path_ to the logs:
   AWSLogs/${Account_ID}/vpcflowlogs/${_time:%Y}/${_time:%m}/${_time:%d}/
5. Enable _Assume Role_
6. Paste the _AssumeRole ARN_ created in the Cloud Trust template.
7. Enter the _External ID_ (if created).
8. Click _Save_
9. _Next Screen_
![Partner Logo](/images/cribl11.png)

## Set Up Collectors and AWS Sources - 2

![Partner Logo](/images/cribl12.png)


1. Name the _Collector_ (e.g. cloudtrail )
2. Name the _S3 bucket_ (If the name of the bucket contains dashes (-), plase single quotes (‘’)around the name to avoid error messages.)
3. Select the _Region_ of the S3 bucket
4. Set the _Path_ to the logs:
   AWSLogs/${Account_ID}/CloudTrail/${_time:%Y}/${_time:%m}/${_time:%d}/
5. Enable _Assume Role_
6. Paste the _AssumeRole ARN_  created in the Cloud Trust template.
7. Enter the _External ID_ (if created).
8. Click _Save_
9. _Commit and Deploy_

    ![Partner Logo](/images/cribl13.png)



## Set Up S3 Sources
Click _Sources_ -> _Amazon S3_ -> _Add New_ and enter the following:

![Partner Logo](/images/cribl14.png)

1. Name the _Collector_ (e.g. cloudtrail )
2. Input _SQS Name_
3. Enter the _Region_
4. Click _Assume Role_
5. _Next Screen_
6. Setting up Assume Role Authentication
  ![Partner Logo](/images/cribl15.png)
7. _Enable for S3_ - yes, and _Enable for SQS_ - yes
8. Enter Target _AWS Account ID_
9. Enter the _AssumeRole ARN_ ID from the Cloud Trust CloudFormation Template
10. Enter _External ID_
11. Click _Save_
12. __Commit and Deploy
  ![Partner Logo](/images/cribl16.png)






{{% notice warning %}}
The examples and sample code provided in this workshop are intended to be consumed as instructional content. These will help you understand how various AWS services can be architected to build a solution while demonstrating best practices along the way. These examples are not intended for use in production environments.
{{% /notice %}}
