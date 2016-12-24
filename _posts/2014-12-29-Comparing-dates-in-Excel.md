---
layout: post
author : Mechanical Object
category: Article
title: Comparing dates in Excel
tagline: 
tags: [date comparison, excel, project]
published : true
---
On my project, I am not 100% on development. My job also requires preparation of integration tests which consist of getting an excel file from _business people_, extract the information and compare them with the information the system generates. 

<!--more-->

I encountered a strange and silly problem today while working on excel: I needed to validate if two columns have the same value. On the right side, I have a value formatted like :

{% raw %}
20/03/2014
{% endraw %}

and on the left side I have another date value formatted like :

{% raw %}
20-03-2014
{% endraw %}

When I did

{% raw %}
=A1 = A2
{% endraw %}

the result was false. A colleague of mine suggested the following and it works :

{% raw %}
=DATE(YEAR(A2); MONTH(A2);DAY(A2))=DATE(YEAR(A1); MONTH(A1);DAY(A1))
{% endraw %}
