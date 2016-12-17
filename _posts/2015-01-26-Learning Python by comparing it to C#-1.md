---
layout: post
author : Mechanical Object
category: Article
title: Learning Python by comparing it to C#-1
tagline: 
tags: [c#,programming,programming,python,python]
published : false
--- 
"I decided to learn Python. Multiple reasons :

*   New year, new language _-why not?-_. I love programming languages.
*   On the market in France, I observe an increasing number of job offer for Python developers. It can open me unexpected doors if I have, one day, a sudden change of heart.
*   I have a keen interest on **Machine Learning**. As of today, I have the impression that Python became **the unofficial language** for this domain along with **R**. A lot of resources uses python programs or code snippets to demonstrate algorithms and different calculations.

<!--more-->

I confess. The most serious reason that motivates me is the last one. My goal is not to become proficient in Python or become a professional Python developer. I just want to be able to read Python code by learning the bases. The approach I am taking to reach my goal in a _-most time efficient way-_ is to take the basics of the programming language I know well _-it will be C# for this purpose-_ write a code snippets with it and try to do the same with Python. If it works, in the future, I will most probably target either Ruby or F# with the same approach. How to describe a programming language ? How to find notions that can identify most of the programming languages so that I can list these notions for C# and compare to Python?

## Language Identity

### How to define C# ?

<a href="" https:="" en.wikipedia.org="" wiki="" c_sharp_(programming_language)""="">Wikipedia</a> states :

> C# (pronounced as see sharp) is a multi-paradigm programming language encompassing strong typing, imperative, declarative, functional, generic, object-oriented (class-based), and component-oriented programming disciplines.

<a href="" http:="" www.microsoft.com="" en-us="" download="" details.aspx?id="7029&quot;&quot;">Official C# specifications</a> indicates :

> C# (pronounced â€œSee Sharpâ€) is a simple, modern, object-oriented, and type-safe programming language ... C# is an object-oriented language, but C# further includes support for component-oriented programming.

Honestly what I was looking was an official information (on msdn page or in the C# specifications) describing the language with common words as wikipedia does.

### How to define Python ?

From <a href="" https:="" en.wikipedia.org="" wiki="" python_(programming_language)""="">Wikipedia</a> :

> Python is a widely used general-purpose, high-level programming language.[17][18][19] Its design philosophy emphasizes code readability, and its syntax allows programmers to express concepts in fewer lines of code than would be possible in languages such as C++ or Java.[20][21] The language provides constructs intended to enable clear programs on both a small and large scale.[22] Python supports multiple programming paradigms, including object-oriented, imperative and functional programming or procedural styles. It features a dynamic type system and automatic memory management and has a large and comprehensive standard library.[23]

<a href="" https:="" docs.python.org="" 2="" faq="" general.html#what-is-python""="">From official website</a>

> Python is an interpreted, interactive, object-oriented programming language. It incorporates modules, exceptions, dynamic typing, very high level dynamic data types, and classes. Python combines remarkable power with very clear syntax. It has interfaces to many system calls and libraries, as well as to various window systems, and is extensible in C or C++. It is also usable as an extension language for applications that need a programmable interface. Finally, Python is portable: it runs on many Unix variants, on the Mac, and on PCs under MS-DOS, Windows, Windows NT, and OS/2.

### Can we categorize?

It seems complicated to put these languages into strict categories. There are some obvious differences like _interpreted vs compiled_. There are other points that are more hazy like _strong typing vs dynamic typing_ . The main confusing point is the vocabulary. For example, some say that C# is strongly typed language. Others say it is a statically typed language. I think these fellows want to say the same thing.

```
// all the types are already defined at compile time 
int myInteger = 0;
float myFloat = 0f;
decimal myDecimal = 0d;

var newVariable = myDecimal; //newVariable is a decimal

```

But with the introduction of <a href="" https:="" msdn.microsoft.com="" en-us="" library="" dd264741.aspx""="">`dynamic` keyword</a>, this isn't very true anymore is it ? Let's give tiny example :

```
dynamic dynamicInt = 3;
int myInt = 2; 
object myObject = 8;

...

dynamicInt = dynamicInt -1; // no problem on compile time
myInt = myInt * 2; // ok
myObject = myObject -8; // KO , this won't compile

```

So the attempt to fully categorize fail, I leave this point aside. I'll discover more as I advance in the topic and the differences will get clearer in my mind by coding samples.

### What is next?

I need a roadmap that'll trace the very basics I am looking forward to learn. So in the next article, I'll share my small and modest roadmap.