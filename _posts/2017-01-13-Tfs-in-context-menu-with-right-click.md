---
layout: post
author : Mechanical Object
category: Article
title: Tfs context menu with right click
tagline: 
tags: [tfs, context menu, registry, windows]
published : true
--- 

I lost the context menu entries for TFS which are quite handy especially when you 
want to add multiple files.

<!--more-->

I found [the answer on SO](http://stackoverflow.com/q/10442099){:target="_blank"}

* Open the registry
* Go to : HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\explorer \ShellIconOverlayIdentifiers
* Prefix any "Tfs" folder with numbers (“1TfsOverlayAdd” , “2TfsOverlayEdit” etc.). This allows them to take priority over other icon overlays.
* Restart the machine or kill explorer.exe and restart it