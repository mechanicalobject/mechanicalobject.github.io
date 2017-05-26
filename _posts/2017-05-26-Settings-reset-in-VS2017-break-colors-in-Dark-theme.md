---
layout: post
author : Mechanical Object
category: Article
title: Settings reset in VS2017 break colors in Dark theme
tagline: 
tags: [vs2017, dark theme, color problem]
published : true
--- 

After I updated VS 2017, I opened my solution and the code was unreadable because of strange text colors that did not belong to original dark theme.

I looked up for this issue on the internet, it seems folks had [the same problem](https://msdn.microsoft.com/en-us/library/ff963553.aspx){:target="_blank"}

The following steps indicated in the discussions seems to solve the problem : 

1/ Set fonts & colors to default
2/ Change theme from light to dark (or from dark to light)
3/ Restart Visual Studio