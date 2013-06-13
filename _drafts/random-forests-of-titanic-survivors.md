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
* Siblings & spouses aboard (integer)
* Parent & children aboard (integer)
* Ticket number
* Fare
* Cabin
* Port of embarkation

## My approach

Since this is meant as a training project, Kaggle provides some introductory advice on training an algorithm for a few environments like Excel and Python.  I decided I would skip those and see how well I could do on my own with some help from my trusty friend [R](http://www.r-project.org/).  My initial approach is based on the wonderfully-versatile [random forest](http://en.wikipedia.org/wiki/Random_forest) technique.  As [yhat](http://blog.yhathq.com/posts/random-forests-in-python.html) have pointed out recently, _"Random forest is the Leatherman of learning methods.  You can throw pretty much anything at it and it'll do a serviceble job"_.  That's a great first technique for this project since I'm trying to get an initial idea of how deep the problem is in a quick fashion.  Here's my initial process as an outline:

* Read training set file into memory
* Do any data cleanup needed
* [Factorize](http://www.stat.berkeley.edu/classes/s133/factors.html) those data columns that are really factors
* Train my random forest using the in-memory training set
* Observe the error estimate from the forest model
* If everything looks good, produce predictions for the Kaggle-provided test set, and write them in Kaggle's preferred output format

Here's the code that performs the steps listed above.  We'll discuss what the code is doing below.

{% gist 5773202 %}

The first few lines are very pedestrian; we read the file in and set column types.

``` r
library(randomForest)
 
passengers <- read.csv("~/code-projects/kaggle/titanic/data/train.csv",
                      comment.char="", quote="\"", sep=",", header=TRUE, stringsAsFactors=FALSE,
                      colClasses=c("integer", "integer", "character", "character", "numeric", "integer", "integer", "character", "numeric", "character", "character"))
```


Next, there are a few rows that have no port of embarkation specified.  We should really address these, but for now we'll remove them from our training set.  The _prepFeatures_ code (and the code directly above it) performs that factorization I mentioned in the process outline; there are a small number of _pclass_, _sex_, _embarked_, and _survived_ values, so let's treat them as such.  Notice that some people have no age; we should determine if we can estimate the age based on other factors, but for now let's just give them all the same arbitrary age.

``` r
# TODO - should we keep these rows?
passengers <- passengers[which(passengers$embarked != ""),]
 
passengers$survived <- as.logical(passengers$survived)
passengers$survived <- factor(passengers$survived)
 
prepFeatures <- function(df) {
  # Let's treat enumerations as factors
  df$pclass <- factor(df$pclass)
  df$sex <- factor(df$sex)
  df$embarked <- factor(df$embarked)
  
  # TODO - Fixing missing ages
  df[which(is.na(df$age)),]$age <- 99
  
  return(df)
}

passengers <- prepFeatures(passengers)
```


Now comes the meat of the code:  We define a function that will return a random forest model trained on provided data.  We use the _randomForest_ method from the [CRAN package of the same name](http://cran.r-project.org/web/packages/randomForest/index.html).  We then identify the columns of data that we would like to include in the model, train, and observe the console output.

``` r
trainForestModel <- function(trainingFrame, cols) {
  model <- randomForest(survived ~ ., data=trainingFrame[, c("survived", cols)])
  print(model)
  return(model)
}
 
# So let's create a model
modelCols <- c("pclass", "sex", "embarked", "fare", "sibsp", "parch")
model <- trainForestModel(passengers, modelCols)
```

Let's look at the console output from R at this point:

```
Call:
 randomForest(formula = survived ~ ., data = trainingFrame[, c("survived",      cols)]) 
               Type of random forest: classification
                     Number of trees: 500
No. of variables tried at each split: 2

        OOB estimate of  error rate: 18.56%
Confusion matrix:
      FALSE TRUE class.error
FALSE   507   42  0.07650273
TRUE    123  217  0.36176471
```

What the console is telling us here is that we are building a _classification_ type of random forest (as opposed to a [regression](http://en.wikipedia.org/wiki/Regression_analysis)).  We are building 500 (the default) trees in our forest.  The model's estimate is that our [OOB (out-of-bag) error rate](http://www.stat.berkeley.edu/~breiman/RandomForests/cc_home.htm#ooberr) is 18.56%.  You will notice that unlike other machine learning techniques like a linear regression, we don't need to separate our training data into training and test data to determine approximate error rate; the properties of the model (namely that only a subset of information is used to train each tree) are such that we can use remaining test cases from each training set to test the overall model.  Also worth noting is the [confusion matrix](http://en.wikipedia.org/wiki/Confusion_matrix) of the model's estimate: apparently we are very good at predicting when someone did not survive (~ 92.3% accuracy), but need some work in predicting when someone survived (~ 63.8% accuracy).

Now we load the real test data from Kaggle without any survivorship data, and run it through our trained model.

``` r
# And load Kaggle's test data
testdata <- read.csv("~/code-projects/kaggle/titanic/data/test.csv",
                       comment.char="", quote="\"", sep=",", header=TRUE, stringsAsFactors=FALSE,
                       colClasses=c("integer", "character", "character", "numeric", "integer", "integer", "character", "numeric", "character", "character"))
 
testdata <- prepFeatures(testdata)
 
# And predict their results
prediction <- predict(model, newdata=testdata, type="class")
```

Nothing to really note here.  Finally, Kaggle wants to see predictions as a single file with a header line, followed by a series of 1's and 0's indicating survivorship.  We'll write these by using R's _write.table_ function, but first we have to coalesce the logical values into integers.  We do that with a neat little trick by adding 0 to their logical values.

``` r
# And let's write our prediction results
out.frame <- data.frame(survived=as.logical(prediction)+0)
 
# TODO - if there are any NAs let's assume they didn't survive for now
out.frame[which(is.na(out.frame$survived)),] = 0
 
write.table(out.frame, file="~/code-projects/kaggle/titanic/prediction.tsv", sep=",", quote=FALSE, col.names=TRUE, row.names=FALSE)
```

Now we've got a file that's in Kaggle's preferred format, ready for submission to be graded!  Let's see how we did:

