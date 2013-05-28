---
layout: default
title: Applied combinatorics and Legos
tags: R legos combinatorics data-science
---

The following is a blogged version of a 5-minute lightning talk I gave recently at a [Data Science Indy](http://www.meetup.com/dsindy/) meetup.  The title of the talk was _Applied Combinatorics or: How much do I have to spend to get the Chicken Suit Guy?_

![Lego Series 9 Minifigures](http://cache.lego.com/e/dynamic/is/image/LEGO/71000?$main$)

I love Legos.  More specifically, my 2 year-old son loves Legos, and I love playing with him.  One of our favorite things about Legos are their minifigures.  Lego has these neat rotating series of minifigs (as they're called) where they create some unique figs and sell them in "blind bags" so that you don't know which figure in the series you are getting.  Pictured above is the *Series 9* collection, which is comprised of 16 unique figures like _Mr. Good and Evil_, _Heroic Knight_, and _Chicken Suit Guy_.

A store near our house sells these bags for $3, and we wanted to get _Chicken Suit Guy_.  The question is, how do we get him **without** looking like a creepy person in the store that is feeling up all of the lego bags?  One option would be to just start buying bags until we get him.  In fact, that's what his mom and Grandmother wanted to do.  Me being the nerdy fun killer I knew there was a smarter, albeit more math-involved, way to get _Chicken Suit Guy_ without breaking the bank.  Math to the rescue!  Here's the breakdown:

### Problem
At first we wanted _Chicken Suit Guy_, but we didn't want to spend any more money than we had to.  That eventually turned into us wanting all 16 figures with spending the least amount of money.

### Known Facts
* Blind bags cost $3 (each).
* The figure inside a blind bag is random.
  * The degree of randomness is unknown (some figures appear to be more rare than others), but all figures in this series appear to be reasonably common.
* You can go on eBay and buy figures like _Chicken Suit Guy_, typically for about $7.

### Approach
In [combinatorics](https://en.wikipedia.org/wiki/Combinatorics), this is a variation of the classic [coupon collector's problem](http://en.wikipedia.org/wiki/Coupon_collector's_problem).  Simply put, given _n_ minifigs, how many minifigs do I expect to buy before getting all of them.  Since this was going to be presented at a data science meetup, my first thought was to talk about [logarithms](http://en.wikipedia.org/wiki/Logarithm) and [harmonic numbers](http://en.wikipedia.org/wiki/Harmonic_number), but I was told there were a fair amount of people at the meetup who were more data science-curious, so I instead decided to base my talk on the output of running a large number of samples and taking the average as a determining factor.  I would leave something for the stat/probability geeks in the house though, as I'd do my simulations in [R](http://www.r-project.org/).

Below is R code that defines a series of functions to approximate how many purchases it would take to collect a distinct set of all figures of size _n_, how many purchases would be needed to finish a set of size _n_ given a starting size _seen_, and how many purchases it would take to see the next unique figure, again given set size _n_ and starting size _seen_.  At the bottom of the code, we do an example trial run.

<script src="https://gist.github.com/stetzer/5650861.js"></script>

An example run of the code yields the following results:

![Money spent to acquire minifigs](/images/2013-05-28-applied-combinatorics-and-legos/money-spent-to-acquire-minifigs.png)

| Total Figures | Unique Figures | Money Spent |
| ------------- | -------------- | ----------- |
|1|1|3|
|2|2|6|
|3|3|9|
|4|4|12|
|5|5|15|
|6|6|18|
|7|6|21|
|8|7|24|
|9|8|27|
|10|8|30|
|11|9|33|
|12|9|36|
|13|10|39|
|14|11|42|
|15|11|45|
|16|11|48|
|17|11|51|
|18|11|54|
|19|12|57|
|20|12|60|
|21|12|63|
|22|12|66|
|23|12|69|
|24|12|72|
|25|12|75|
|26|12|78|
|27|12|81|
|28|12|84|
|29|12|87|
|30|12|90|
|31|12|93|
|32|12|96|
|33|12|99|
|34|12|102|
|35|12|105|
|36|12|108|
|37|12|111|
|38|12|114|
|39|12|117|
|40|13|120|
|41|13|123|
|42|14|126|
|43|14|129|
|44|14|132|
|45|14|135|
|46|14|138|
|47|15|141|
|48|15|144|
|49|15|147|
|50|15|150|
|51|15|153|
|52|15|156|
|53|15|159|
|54|16|162|

### Conclusions
What conclusions did I draw from my simulations?

* It will take about 54 purchases, or $162, on average to collect a complete set of all 16 minifigs.
  * Going to eBay and buying each fig for $7 each is actually cheaper, with a total cost of around $112.
* If I switch to eBay purchasing after collecting 9 figures, I will optimize my total cost down to ~ $57.

$3.56 / figure is pretty good considering a starting cost of $3 for a random selection!

#### Addendum
As one meetup member pointed out, I am not assigning any value to duplicates that we collect.  Honestly that's mainly a factor of my son being little and losing his toys all over the house, so duplicates are good for spare parts :)