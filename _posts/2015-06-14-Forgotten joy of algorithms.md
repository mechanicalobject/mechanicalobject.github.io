---
layout: post
author : Mechanical Object
category: Article
title: Forgotten joy of algorithms
tagline: 
tags: [algorithms, python, F#, edx, coursera, mit, computer science]
published : false
---
<a href="" target="_blank"></a>
All began with my motivation to learn Python. I began to read the <a href="" target="_blank">specification</a>, following  <a href="" target="_blank">online tutorials</a>. But with 8 hours of work day with intensive C# coding, I never achieved the level I wished to have. 

I tought about *-how I can learn this programming language-* and what I needed is a frame or a well defined context if you wish. This context should motivate me, there should be calendar that I can respect, it should'nt be to hard to follow and I cannot give more than 4 hours per week.

So I decided to re-try the same schema I tried with Matlab. My recent experience with <a href="https://www.coursera.org/course/matlab" target="_blank">**Introduction to Programming with MATLAB**</a> course on <a href="https://www.coursera.org/" target="_blank">Coursera</a> was quite a success. There was a well defined context, the lectures weren't too difficult to follow, topics are very well explained and there were a lot of interesting, challenging and fun programming exercices *-like calculate scores of a bowling game-*. Also the lecture and programming exercices didn't necessitate more than 4 hours a week. Every week, during these 4 hours I watched approxamately 1 hour of video lecture and I spent 3 hours on programming exercices. At guess what? **I am now able to write programs using Matlab** :) . I am not an expert that is certain but the the goal I fixed to myself which was being able to write and reading other's code in Matlab is achieved.

So by looking over the web, I found <a href="https://www.edx.org/course/introduction-computer-science-mitx-6-00-1x-0" target="_blank">Introduction to Computer Science and Programming Using Python</a>. That seemed attractive to me because I really like learning programming languages using an algorithmic approach. But I had doubts before enrolling to this class on the requiered level since it was an introductory level course. Anyway, I have enrolled , first week is a bit boring for people having already a background on the field but the second week was a perfect introduction to Python. 

During the 2nd week, the instructor took the following as a problem : **"Calculate square (or cubic) root of a number'**. This simple problem opened up a discussion on :

* guess and check methods, successive approximation
* bisection search (dichotomy method)
* brief presentation of Newton-Raphson method. 

I used in the past dichotomy method on a given set of objects like a list of employees or a list of integers, doubles like the following:

```csharp
var list = new List<double>();
list.Add(1.2d, 5d, 853.2215d, ..., 124545d); // finite set
```

When you talk about a list of objects, you have immediately a finite set and you do not need approximation. You can try brute force search algorithm to loop over the list and to check each element to see if it is the one you want or you can divide the list each time by 2 to diminish search space hence to lower the complexity from O(n) to O (log(n)).

Main intresting thing using this set of algorithms (successive approximation, bisection search, Newton-Raphson) on calculating square (or cubic) root of a number which can be a floating point number or an integer, is that the set in which you are working is infinite. Imagine that I want to calculate square root of 4.5, the only information I have with basic mathematic knowledge is that the solution is a real number and it is somewhere between 0 and 4.5.

```
x € [0,4.5[ where x € R is an infinite set.
```

