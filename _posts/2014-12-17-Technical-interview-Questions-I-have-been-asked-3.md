---
layout: post
author : Mechanical Object
category: Article
title: Technical interview &#58; Questions I have been asked-3
tagline: 
tags: [.net, c#, technical interview]
published : true
---
In the [previous article]({{ site.baseurl }}{% post_url 2014-12-12-Technical-interview-Questions-I-have-been-asked-2 %}){:target="_blank"},
of the series on technical interview questions, I shared the real interview questions I have been asked for middle-sized company that 
commercialize HR solutions. I have gone through my mails and found a memo to myself for a series of interviews I had for job vacancy 
in a small arbitrage company. 

<!--more-->

**It was the best technical interview I had so far**. Unfortunately, I failed in the last interview (_-I had 3 technical interview sessions with this company-_). This is a good example of interview where you learn a lot. Fellow I had interview with has a very solid background and the way he prepared the interview aimed to screen the candidate on different aspects using direct knowledge questions, discussions about a large and vague topics, puzzles etc.

*   Write a method that multiplies 2 integers without using * operator
*   What is the difference between 1 byte and 1 bit ?
*   What is the default visibility of a class ?
*   What is the length of a char in bits ? What about unsigned char ?
*   What is the last element of a chain of characters ?
*   What is the type of the result when you divide an integer by a double ?
*   What is the difference between `int` and `Int32`
*   Which of the following types is not a reference type ?
    *   `Delegate`
    *   `IDisposable`
    *   `Object`
    *   `Int32`
*   Which of the following exceptions cannot be caught -_generally_-?
    *   InvalidProgramException
    *   NullReferenceException
    *   NotImplementedException
    *   StackOverflowException
*   A unit test lets
    *   to test a module via GUI
    *   to test an isolated method
    *   to test a method and underlying layers it depends to
    *   to test the persistence
*   What is dependency injection ?
*   Do you follow any blogs ?
*   What is the last technical book you have read or you are reading ?
*   Difference between `Decimal` and `Double`, which one is faster ?
*   How the garbage collector works ?
*   Describe and state the difference _-if comparable-_ of the following notions
    *   processor
    *   process
    *   thread
*   Have you heard about continuous integration ?
*   Difference between `struct` and `class`.
*   When you have the following code, the field `Id` is kept in the heap or in the stack ?

{% highlight csharp %}

public class User
{
   public int Id {get;set;}
}
....
{
   ...
   var user = new User(){Id = 1};
}

{% endhighlight %}


*   How `List<t>` is implemented ?
*   Have you heard about C.Q.R.S ?
*   Have you heard about R.E.S.T ?
*   Difference between web service and messaging ?
*   Write FizzBuzz on paper?
*   I have 8 balls. All balls have same weight except one which is heavier. How many weighing should I do to spot the heaviest ball ?
*   Imagine you observed a performance problem on the software you working on. How would you proceed to spot the problem ?
*   What happens at the moment I write google.com on navigation bar and hit enter ?
*   Do Html pages contain images ?
*   Refactor a _-heavily damaged-_ windows forms application.