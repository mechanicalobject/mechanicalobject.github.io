---
layout: post
author : Mechanical Object
category: Article
title: Learning Python by comparing it to C#-2
tagline: 
tags: [c#,programming,programming,python,python]
published : false
--- 
"<a href="" http:="" blog.mechanicalobject.com="" 2015="" 01="" 25="" learning-python-by-comparing-it-to-csharp-1""="">In the previous article</a>, I shared the reason of my motivation and made a **failed** attempt to categorize languages to better define the context. I need a roadmap. This roadmap can slightly change by digging deeper if subjects gets my attention, but the skeleton of the roadmap should stay still.

<!--more-->

## Roadmap

There are several programming languages created since 1950s. When I was in the engineering school, I remember that professors were pitiless. They were giving us a lot of projects and almost each of them was to realize using a different programming language. I have done projects in Lisp, Prolog, R, Java, C, C++, C#, VB.net. They didn't care whether we mastered the programming language _-which was impossible by the way given the due date of the projects-_. I realized that despite the fact that all these languages have different syntax and support different programming paradigms and sometimes necessitates different logical approach (like Prolog), there are always some common bases. Ever since we are assigning variables, making operations on them, doing comparisons to express conditions given in the requirements and looping over collection to make iterative tasks. _-Uncle Bob coins this in one of his talks, I'll update this post if can find a link to the talk!-_ Based on this observation and on the fact that I'll need tools and some documentation, I came up with the following roadmap:

| Step | Status | Notes |
| --- | --- | --- |
| Which tools to use ? |
| Where is the documentation, where I can get some help ? |
| How to recognize a file that contains Python source code ? |
| What are the language keywords |
| How to write an output? _-famous hello world-_ |
| How to assign a variable â‡’ study of basic underlying types |
| How to do arithmetic operations? (*,+,-,/,Power etc.) |
| How to do casts ? |
| How to use external libraries, how to organize code? |
| How to use logical operators ? |
| How can I use lists ? |
| How can I do operations on string types? |
| How can I do comparaisons ? (_-if else-_) |
| How can I loop over a collection ? |
| How can I define a method ? |
| How can I define a class ? |

This is a primary roadmap that'll help me to get me to my comfort zone. This roadmap will expand later when I'll try to combine what I will learn to create more and more complicated instruction blocks.

## Let's begin!

### Which tools to use ?

*   For C# code snippets : I'll use <a href="" http:="" www.linqpad.net="" ""="">LINQPad</a> to keep things simple simple _-because I don't want to open a visual studio project for every code snippet-_.
*   For Python code snippets :

    *   First I downloaded the latest version of python from the <a href="" https:="" www.python.org="" downloads="" ""="">official web site</a>. Please note that the moment I write this article, the latest version of python is **3.4.2.** Python comes with an interpreter that you can access via an command line. I won't use this despite the fact that almost in every tutorial you'll find on the web this interpreter is used. _-or other more evolved interpreters like <a href="" http:="" ipython.org="" ""="">IPython</a>.-_
    *   Second, I looked on the web for a suitable and free IDE to use. I found the followings

| Name | Link | Notes |
| --- | --- | --- |
| WingIDE | <a href="" http:="" www.wingware.com="" ""="">http://www.wingware.com/</a> | there are 3 different versions : 
* Professional (expensive) 
* Personal (less expensive) 
* 101 (free but misses a lot of features |
| PyDev | <a href="" http:="" pydev.org="" ""="">http://pydev.org/</a> | PyDev is a Python IDE for Eclipse, which may be used in Python, Jython and IronPython development. |
| Eric | <a href="" http:="" eric-ide.python-projects.org="" ""="">http://eric-ide.python-projects.org/</a> | Open source & free of charge |
| Spyder | <a href="" https:="" pythonhosted.org="" spyder="" ""="">https://pythonhosted.org/spyder/</a> 
<a href="" https:="" code.google.com="" p="" spyderlib="" ""="">https://code.google.com/p/spyderlib/</a> 
<a href="" https:="" bitbucket.org="" spyder-ide="" spyderlib="" src""="">https://bitbucket.org/spyder-ide/spyderlib/src</a> | Spyder (previously known as Pydee) is a powerful interactive development environment for the Python language with advanced editing, interactive testing, debugging and introspection features |
| PyCharm | <a href="" https:="" www.jetbrains.com="" pycharm="" ""="">https://www.jetbrains.com/pycharm/</a> | Community edition can be downloaded and used free of charge |
| IPython Notebook | <a href="" http:="" ipython.org="" notebook.html""="">http://ipython.org/notebook.html</a> | The IPython Notebook is a web-based interactive computational environment where you can combine code execution, text, mathematics, plots and rich media into a single document |
| anaconda CE | <a href="" https:="" store.continuum.io="" cshop="" anaconda""="">https://store.continuum.io/cshop/anaconda</a> | It seems this is a quite popular distribution in scientific world (research labs, universities, phd students) 
**From the web site** : 
Completely free enterprise-ready Python distribution for large-scale data processing, predictive analytics, and scientific computing |

Every C# developer that have used Resharper or Java developers that coded using IntelliJ knows JetBrains and their contribution to programming world. I went on the site and downloaded PyCharm community edition ( small tip : I am not disappointed at all!)

### Where is the documentation, where I can get some help ?

From the documentation point of view, I think Python developers are lucky fellows. 
<a href="" https:="" www.python.org""="">The official web site</a> is very easy and 
pleasant to naviguate and there is a huge 
<a href="" https:="" www.python.org="" doc="" ""="">documentation section</a> 
separated by versions _-a small combobox helps the visitor to change the version-_. 
There are several documentation per level, e.g a document for beginners , for 
advanced topics , links to books and a complete language and library references.

### How to recognize a file that contains Python source code ?

Files that contains Python source code has the ```.py``` extension. Every line of code is 
executed except the comments.

{% highlight python %}

// commented line

/*
Multiple
line of 
comment goes here
*/

{% endhighlight %}


{% highlight python %}

# commented line

'''
Multiple
line of 
comment goes here
'''

{% endhighlight %}


### What's next ?

In the next article, I'll discover keywords of Python programming language. Then I'll 
write code snippets in C# and try to reproduce the same behavior using Python by 
following the roadmap.