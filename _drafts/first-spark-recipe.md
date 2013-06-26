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
URL     ftp://38.107.129.5/Training/Training%20Documentation/Latitude%20V6.2%20Training%20Binder/06%20Latitude%206%202%20Release%20Notes_Build%2027.pdf
MENTION Microsoft       80679   http://en.wikipedia.org/wiki/Microsoft
MENTION Microsoft       134415  http://en.wikipedia.org/wiki/Microsoft
MENTION Windows Server 2008     80862   http://en.wikipedia.org/wiki/Windows_Server_2008
MENTION Windows Server 2008     134744  http://en.wikipedia.org/wiki/Windows_Server_2008
MENTION Windows 7       81028   http://en.wikipedia.org/wiki/Windows_7
MENTION Windows 7       134910  http://en.wikipedia.org/wiki/Windows_7
MENTION operating systems.      81109   http://en.wikipedia.org/wiki/Operating_system
MENTION Windows Vista   134573  http://en.wikipedia.org/wiki/Windows_Vista
TOKEN   Fresh   54828
TOKEN   evidence        32081
TOKEN   Allow   72597
TOKEN   operator        148693
TOKEN   notice  507684
TOKEN   save    77567
TOKEN   subfolder       154988
TOKEN   PELCO   490470
TOKEN   crashed 301434
TOKEN   audit   296060
```

Let's use Spark and Scala to do something rather trivial: extract the mentioned terms in the entire Wikilinks corpus, count them, and show the top-mentioned terms.  Here's the code to do just that:

```scala
val lines = sc.textFile("<some path>/data*")
val mentions = lines.filter(l => l.startsWith("MENTION"))
val counts = mentions.map{l => val txt = l.split("\t")(1); (txt, 1)}.reduceByKey((a, b) => a + b)
val sorted = counts.map{case (k, v) => (v, k)}.sortByKey(false, 8)
sorted.take(10).foreach(println _)
```

Which provides the following (trimmed) output:

```
(79855,Flickr)
(65797,stub)
(45246,United States)
(31134,India)
(29295,World War II)
(28053,God)
(23862,public domain)
(21108,2007)
(20882,disambiguation)
(20697,GNU Free Documentation License)
```
