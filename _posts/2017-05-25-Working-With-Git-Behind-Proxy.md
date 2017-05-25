---
layout: post
author : Mechanical Object
category: Article
title: Working with git behind a proxy
tagline: 
tags: [links]
published : false
--- 
Scenario : Working for a client behind the client proxy 

<!--more-->

Let's say your proxy address is 10.180.34.147:8080

### To manage the proxy only on a particular repo

```git clone https://xxxx/xxxx.git --config "http.proxy=10.180.34.147:8080"```
 
### To manage the proxy for all git repos at global level 

```git config --global http.proxy 10.180.34.147:8080```

The problem with this approach is that you'll be unable to access to your internal git repositories if you have on premises source controller behind the proxy.

### To unset global proxy settings 
 
```git config --global --unset http.proxy```
 
