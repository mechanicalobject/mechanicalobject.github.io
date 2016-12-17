---
layout: post
author : Mechanical Object
category: Article
title: Jekyll installation
tagline: 
tags: [jekyll, blog, github pages]
published : false
---
http://stackoverflow.com/questions/16276049/autofac-how-to-intercept-the-service-with-an-instance-of-a-aspect-but-not-with

""""
Check out the Autofac wiki page on Autofac.Extras.DynamicProxy2. It shows an example of a CallLogger interceptor where it registers a lambda as the interceptor:

var builder = new ContainerBuilder(); 
builder.RegisterType<SomeType>()
       .As<ISomeInterface>()
       .EnableInterfaceInterceptors(); 
builder.Register(c => new CallLogger(Console.Out));
var container = builder.Build();
var willBeIntercepted = container.Resolve<ISomeInterface>();
For your case, just switch it to register an instance.

var builder = new ContainerBuilder(); 
builder.RegisterType<SomeType>()
       .As<ISomeInterface>()
       .EnableInterfaceInterceptors()
       .InterceptedBy(typeof(Aspect));
var interceptor = new Aspect();
builder.RegisterInstance(interceptor);
var container = builder.Build();
var willBeIntercepted = container.Resolve<ISomeInterface>();
Alternatively, you can use named interceptors if you don't want your aspect to be typed.

var builder = new ContainerBuilder(); 
builder.RegisterType<SomeType>()
       .As<ISomeInterface>()
       .EnableInterfaceInterceptors()
       .InterceptedBy("my-aspect-instance");
var interceptor = new Aspect();
builder.RegisterInstance(interceptor)
       .Named<IInterceptor>("my-aspect-instance");
var container = builder.Build();
var willBeIntercepted = container.Resolve<ISomeInterface>();
Again, check out the wiki - there are lots of ways to associate the interceptor with the class being intercepted, including named, typed, attributes... lots of samples on the wiki.

"""