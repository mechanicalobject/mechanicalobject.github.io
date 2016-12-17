---
layout: post
author : Mechanical Object
category: Article
title: Learning Python by comparing it to C#-3
tagline: 
tags: [c#,programming,programming,python,python]
published : false
---
"<a href="" http:="" blog.mechanicalobject.com="" 2015="" 01="" 25="" learning-pythoâ€¦it-to-csharp-2""="">In the previous article</a> of this series, I shared my roadmap, the results of my search on tools and talked about documentation. It's time to attack now to the key concepts of the language that I defined on the roadmap.

### Let's update the roadmap

| Step | Status | Notes |
| --- | --- | --- |
| Which tools to use ? | OK | article nÂ°2 |
| Where is the documentation, where I can get some help ? | OK | article nÂ°2 |
| How to recognize a file that contains Python source code ? | OK | article nÂ°2 |
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

### What are the language keywords

Interesting enough, on the official documentation _-unless I missed something-_ I didn't find a comprehensive table of keywords reserved to the language. For C#, it is quite easy to find all the <a href="" https:="" msdn.microsoft.com="" en-us="" library="" x53a06bb.aspx""="">keywords on the MSDN</a>. But I <a href="" https:="" docs.python.org="" 3.4="" library="" keyword.html""="">discovered an interesting thing</a>. To display all the keywords of python for the installed version the following code snippets does the job :

```
import keyword
print(keyword.kwlist)

```

and the result is :

```
['False', 'None', 'True', 'and', 'as', 'assert', 'break', 'class', 'continue', 'def', 'del', 'elif', 'else', 'except', 'finally', 'for', 'from', 'global', 'if', 'import', 'in', 'is', 'lambda', 'nonlocal', 'not', 'or', 'pass', 'raise', 'return', 'try', 'while', 'with', 'yield']

```

### How to write an output? -famous hello world-

*   C#

```
Console.WriteLine(""Hello World"");

```

*   Python

```
print(""Hello World"")

```

What if I don't want the cursor to go to the new line ? Let's look at the signature of <a href="" https:="" docs.python.org="" 3.3="" library="" functions.html#print""="">the print function</a> :

```
print(*objects, sep=' ', end='n', file=sys.stdout, flush=False)

```
> Print objects to the stream file, separated by sep and followed by end. sep, end and file, if present, must be given as keyword arguments. All non-keyword arguments are converted to strings like str() does and written to the stream, separated by sep and followed by end. Both sep and end must be strings; they can also be None, which means to use the default values. If no objects are given, print() will just write end. The file argument must be an object with a write(string) method; if it is not present or None, sys.stdout will be used. Whether output is buffered is usually determined by file, but if the flush keyword argument is true, the stream is forcibly flushed. Changed in version 3.3: Added the flush keyword argument.

In C# we have `Console.Write` method but in Python there isn't another method(function) name for that, we just pass an optional parameter as the following code snippet suggests.

*   C#

```
Console.Write(""Hello "");
Console.Write(""World);

```

*   Python

```
print(""Hello "",end='')
print(""World) 

```

### How to assign a variable -> studying types

This is the part which is a bit disturbing due to dynamic typing. The following is a perfectly valid Python code snippet :

```
a= 5
print(a)
print(type(a))

a = ""say hello""
print(a)
print(type(a))

a = False
print(a)
print(type(a))

```

And it provides the following output

```
5
<class 'int'>
say hello
<class 'str'>
False
<class 'bool'>

```

The equivalent C# code can be written in the following way

```
dynamic a = 5;
Console.WriteLine(a);
Console.WriteLine(a.GetType());

a = ""say hello"";
Console.WriteLine(a);
Console.WriteLine(a.GetType());

a = false;
Console.WriteLine(a);
Console.WriteLine(a.GetType());

```

Which give the following result :

```
5
System.Int32
say hello
System.String
False
System.Boolean

```

<a href="" https:="" docs.python.org="" 3="" library="" stdtypes.html""="">In the official documentation</a>, built-in types are classified like the following (not exhaustive list, I got only the basic types):

*   Truth type : bool
*   Numeric Types : integers, floating point numbers, complex numbers
*   Sequence types
    *   list, tuple, range
    *   Text sequence type : str
    *   Binary sequence types : bytes, bytearray, memoryview
*   Set types : set, frozenset
*   Mapping types : dict

There is one point that got immediately my attention. **Complex number** is a built-in type. Before .Net 4.0 we didn't have this type but .Net 4.0 introduced a <a href="" https:="" msdn.microsoft.com="" en-us="" library="" system.numerics.complex%28v="vs.110%29.aspx&quot;&quot;">complex type that we can use in System.Numerics namespace</a>. Also I note that numeric types are really simple since there only 3 of them. That is not like this in C#, for example to represent floating point numbers we have 3 types : float(single), double, decimal. In addition to all these built-in types, it is possible to build common types by writing classes like C#.

### What's next ?

I'll follow the roadmap to play with numbers, to do arithmetic operations, learn how to use external libraries.