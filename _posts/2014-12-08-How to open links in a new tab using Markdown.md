---
layout: post
author : Mechanical Object
category: Article
title: How to open links in a new tab using Markdown
tagline: 
tags: [blog, markdown, wordpress]
published : true
---
If you are using markdown syntax as I do, the syntax for that is the following

```
[Name or description of the link](http:your_link) |

```

<!--more-->

**Concrete example**

```
[C# 4.0 and beyond .....](http://channel9.msdn.com/Blogs/matthijs/C-40-and-beyond-by-Anders-Hejlsberg) |
```

This is fine but when the visitor clicks on the link, the link will open in the very same page hence you'll force the user to navigate away.The way to make the page open in another tab or window is to use directly HTML code by defining **`target`** attribute to **`"_blank"`**. For example a portion of the text that displays **.Net Talks** in my [Links](http://blog.mechanicalobject.com/links/videos/) page is like the following :

```
## .Net talks

| Language      | What the talk is about ? |
| :-- |:--|
| English       | <a href="http://channel9.msdn.com/Blogs/matthijs/C-40-and-beyond-by-Anders-Hejlsberg" target="_blank">C# 4.0 and beyond by Anders Hejlsberg</a> |
| English       | <a href="http://channel9.msdn.com/Tags/anders+hejlsberg?sort=viewed" target="_blank">All talks of Anders Hejlsberg's including above one on channel9</a>|
```

And the result is

| Language | What the talk is about ? |
| :-- | :-- |
| English | [C# 4.0 and beyond by Anders Hejlsberg](http://channel9.msdn.com/Blogs/matthijs/C-40-and-beyond-by-Anders-Hejlsberg) |
| English | [All talks of Anders Hejlsberg's including above one on channel9](http://channel9.msdn.com/Tags/anders+hejlsberg?sort=viewed) |