---
layout: post
author : Mechanical Object
category: Article
title: Maybe monad in C# Optional
tagline: 
tags: [functional programming, maybe, monad, optional, c#, library]
published : true
--- 

We began to have "craftmanship" meetups (private for the moment) at work. My colleague presented a C# library 
that implements Maybe [monad](https://en.wikipedia.org/wiki/Monad_(functional_programming){:target="_blank"}) named 
[Optional](https://github.com/nlkl/Optional){:target="_blank"}.

From [the github readme page](https://github.com/nlkl/Optional/blob/master/README.md){:target="_blank"} :

>Optional is a robust option/maybe type for C#.
>...
>Optional is a strongly typed alternative to null values that lets you:

>   *   Avoid those pesky null-reference exceptions
>   *   Signal intent and model your data more explictly
>   *   Cut down on manual null checks and focus on your domain