---
layout: post
author : Mechanical Object
category: Article
title: Coverage of auto-implemented properties with dotCover
tagline: 
tags: [.net, auto-implemented properties, c#, dotcover, unit test coverage]
published : true
---
Since I am using dotCover I wondered why every method call is covered properly by tests (if conveniently 
tested) but the auto-implemented properties are always displayed as “not covered at all”. Let's take the 
following ugly tests :

<!--more-->

{% highlight xml linenos%}

[Test]
public void TestStartDateProperty()
{
    var budget = new Budget(new DateTime(2010,1,1),new DateTime(2013,12,31));
    Assert.AreEqual(budget.StartDate, new DateTime(2010,1,1));
}

[Test]
public void TestEndDateProperty()
{
    var budget = new Budget(new DateTime(2010,1,1),new DateTime(2013,12,31));
    Assert.AreEqual(budget.EndDate, new DateTime(2013,12,31));
}

{% endhighlight %}


Now let's look @ their implementations. For StartDate attribute, I used "old fashioned" way and for 
the EndDate, I used automatic properties.

{% highlight xml linenos%}

public Budget(DateTime startDate, DateTime endDate)
{
   _startDate = startDate;
   EndDate = endDate;
}

private readonly DateTime _startDate;
public DateTime StartDate
{
   get {return _startDate;}
}

public DateTime EndDate{get; private set;}

{% endhighlight %}

When I run the tests and asked to see the coverage, I have the following results : 

![Test coverage](http://i.imgur.com/MYO4elnl.png)

It seems that this is already a logged issue on [jetbrains website](https://youtrack.jetbrains.com/issue/DCVR-4103){:target="_blank"}.