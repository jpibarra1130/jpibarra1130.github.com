---
layout: post
title: "Some Notes and Errors Encountered After Trying Out Apache Spark"
description: "Some Notes and Errors Encountered After Trying Out Apache Spark"
tags: [errors, spark, apache, apache spark, notes]
---

I was playing around with [Apache Spark](https://spark.apache.org/) a couple of weeks back. For those of you who are not familiar with Apache Spark, they have a really good documentation which you should read [here](https://spark.apache.org/docs/latest/quick-start.html). You should also check out [UC Berkeley's paper](https://www.cs.berkeley.edu/~matei/papers/2012/nsdi_spark.pdf) on RDD which will help you gain a deeper understanding on how Spark works. Don't be daunted by the paper, it's actually a good read.

Here are a few things I jotted down while working on it.

- to assign a name to master, you will need to create ```conf/spark-env.sh``` and set that value for SPARK_MASTER. Basically, you just need to add this:
	- ```SPARK_MASTER_IP=local.paolo.com```
- You'll be able to view the status of your cluster in in ```http://localhost:8080/```

I have also encountered a few errors when I tried running my application. Here's a list of the errors I've stumbled on together with steps on how I fixed it.

- _Initial job has not accepted any resources_. 
	- Check your cluster UI to ensure that workers are registered and have sufficient memory. I realized that I only have two cores I could connect too, and Spark Shell was already using it up. You can either update your config, or shutdown the other application using the resources. [Source](http://www.datastax.com/dev/blog/common-spark-troubleshooting)
- _java.lang.IllegalStateException: unread block data_.
	- This is most likely caused by versioning issue. In my case, my Mac was running a different version of Scala (v2.11)compared to the one I downloaded (using Scala v2.10). To make this work, I just updated my Mac's Scala version. I did this through:
		- ```brew info scala```
		- ```brew search scala``` - to view the available taps
		- ```brew install homebrew/versions/scala210```
		- If you encounter this: ```bash: scala: command not found``` try running ```brew link homebrew/versions/scala210```
		- ```scala -version```
- _org.apache.spark.serializer.JavaDeserializationStream$$anon$1.resolveClass spark "Class not found"_. 
	- I encountered this when I was trying to run my test application via eclipse. I have a running master in the background so I wanted my test to connect to it instead of spawining its own. To make this work I had to create a fat jar containing all the dependencies. Using Shade plugin from maven will help fix the akka issue too. [Source](http://stackoverflow.com/questions/26892389/org-apache-spark-sparkexception-job-aborted-due-to-stage-failure-task-from-app)
- _ERROR ContextCleaner: Error in cleaning thread java.lang.InterruptedException_. This can be safely ignored. [Source](http://apache-spark-user-list.1001560.n3.nabble.com/Error-in-run-spark-ContextCleaner-under-Spark-1-0-0-td8135.html)

Hopefully, this will help someone out there who is also looking into Apache Spark.