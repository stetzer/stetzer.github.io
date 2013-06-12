---
layout: default
title: Random Forests of Titanic Survivors
tags: R random-forests kaggle data-science titanic
---

The other day I realized I've told countless people about [Kaggle](http://www.kaggle.com/), but I've never actually participated in a competition.  I've always imagined that if I entered a competition, it would consume a good portion of my time and I'd start neglecting other duties.  That said, I decided to enter one this past weekend.  (I'm envisioning my entry like the start of any _Quantum Leap_ episode: "Theorizing that one could time travel within his own lifetime, Dr. Sam Beckett stepped into the Quantum Leap accelerator and vanished.".  Except my version is doesn't involve quantum anything, and while Sam looks like he's talking to himself but he's really talking to Al, I'm just talking to myself.)

<div class="text-center">
  <img src="http://www.staffingdaily.com/wp-content/uploads/2013/04/full-quantum-leap-screenshot.jpg" alt="Stepping into the Quantum Leap accelerator" style="width: 300px; margin-bottom: 20px;"/>
</div>

## The competition

The competition I chose first was _[Titanic: Machine Learning from Disaster](https://www.kaggle.com/c/titanic-gettingStarted)_; their "training" competition.  In a nutshell, Kaggle provides information on 891 Titanic surivors and whether they survived.  Then they provide me with a test set of 418 passengers with the same data except whether they survived.  My job is to train an algorithm to predict whether these 418 people survived, upload my predictions in a format for Kaggle to automatically score, then watch as they rank me on a leaderboard.  Technically there is one additional step where I become obsessed about my position on the leaderboard and start staying up all night to improve things, but we won't talk about that here :)  Here is the info Kaggle provides to me:

* Passenger class
* Name
* Sex
* Age
* Siblings & spouses aboard (count)
* Parent & children aboard (count)
* Ticket number
* Fare
* Cabin
* Port of embarkation

## My approach

Since this is meant as a training project, Kaggle provides some introductory advice on training an algorithm for a few environments like Excel and Python.  I decided I would skip those and see how well I could do on my own with some help from my trusty friend [R](http://www.r-project.org/).  My initial approach is based on the wonderfully-versatile [random forest](http://en.wikipedia.org/wiki/Random_forest) technique.  Here's my initial process as an outline:

* Read training set file into memory
* Do any data cleanup needed
* [Factorize](http://www.stat.berkeley.edu/classes/s133/factors.html) those data columns that are really factors
* Separate my training set into separate training and testing sets
* Train my random forest on a subset of the data columns provided
* Test my algorithm against the test set I created
* If everything looks good, re-train on the entire training set
* Produce predictions for the Kaggle-provided test set, and write them in Kaggle's preferred output format

Here's the code that performs the steps listed above.  We'll discuss what the code is doing below.

{% gist X %}