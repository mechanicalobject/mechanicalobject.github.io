---
layout: post
author : Mechanical Object
category: Article
title: Localization (translation) using resource strings with MVC framework
tagline: 
tags: [.net, c#, data annotations, localization, mvc, resource strings]
published : true
---
A very nice and complete post that I found in [Alex's blog](http://adamyan.blogspot.fr/2010/02/aspnet-mvc-2-localization-complete.html){:target="_blank"}. 
A few concerns though about what is written : 

<!--more-->

1/ I don't believe there is need to write a custom attribute like (unless you want to enhance it)

{% highlight csharp %}
public class LocalizedDisplayNameAttribute : DisplayNameAttribute{}
{% endhighlight %}

I preferred the following for my budget entry model since the base behaviour is OK for me :

{% highlight csharp %}
[Display(Name = "Amount", ResourceType = typeof(Resources.Model.Budget.BudgetEntryStrings))]
public class Budget{}
{% endhighlight %}

2/ I think this might be good idea to create completely new project for resources. Like that, these 
resource files can be managed by an other team or an automatic way of updating them (via a WPF hard 
client application) for example can be found. 

His article gives a handy start point! Thanks...