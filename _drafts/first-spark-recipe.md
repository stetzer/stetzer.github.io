---
layout: default
title: First Spark Recipe
tags: spark scala
---

I'm a huge fan of the [Spark project](http://spark-project.org/).  I've been using it on & off since about January of this year, both in distributed mode as well as just on my local machine.  Speaking as someone who's lived and breathed hadoop for the past several years, I can say there's something very gratifying about asking a question of your (large) data and getting an answer in seconds instead of minutes.  Of course, there are [many](http://blog.cloudera.com/blog/2013/05/cloudera-impala-1-0-its-here-its-real-its-already-the-standard-for-sql-on-hadoop/) [good](http://aws.amazon.com/redshift/) [alternatives](http://www.vertica.com/the-analytics-platform/scale-out-mpp-architecture/) to Spark for in-memory/fast analysis of data, but I happen to like Spark for several reasons:

* It integrates well with the hadoop ecosystem; I can write code that will read from HDFS, HBase, etc.  That's often where my data is to begin with.
* It's free.  Hard to compete with free from a price perspective.
* It's got [Scala](http://www.scala-lang.org/) bindings.  I **really** like [Pig](http://pig.apache.org/), but I've done the round trip of working in Pig Latin, then jumping to Java to create a UDF, then back to Pig enough to last a lifetime.  Of course, I can write my [M/R](http://en.wikipedia.org/wiki/MapReduce) jobs in raw Java, but that's only if you're a glutton for punishment.  [Scalding](https://github.com/twitter/scalding) is also an option, but with Scalding you're still running M/R jobs that spill to disk.
* It's fast.  You'll see below how fast Spark can run.

Let's take Spark for a test drive so I can show you how it performs.

## The Basics

If you want to follow along, I'm assuming you've already [followed the steps to set up a Spark environment](http://spark-project.org/docs/latest/).  Let's start by downloading some a public dataset and performing some basic computations locally.  I've chosen [Google's Wikilinks corpus](http://googleresearch.blogspot.com/2013/03/learning-from-big-data-40-million.html); a set of about 5.5 GB worth of references to English Wikipedia pages.  The format of these (extracted) files looks like:

```
URL ftp://212.18.29.48/ftp/pub/allnet/nas/all60300/ALL60300_UM_V12_EN.pdf
MENTION r NetBIOS 176937  http://en.wikipedia.org/wiki/NetBIOS
TOKEN acting  304940
TOKEN whose 247626
TOKEN capabilities  70039
TOKEN ME  201398
TOKEN calls 514390
TOKEN preferably  346689
TOKEN functionality 358183
TOKEN anything  7034
TOKEN boost 508294
TOKEN enjoying  211878
```

