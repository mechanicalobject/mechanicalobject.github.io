---
layout: post
author : Mechanical Object
category: Article
title: Connection strings and ConfigurationWrapper
tagline: 
tags: [.net, c#, programming, t.d.d, unit test, xml]
published : true
---
I always forget how to properly do these damn things :). Everytime I need to use a connection string, I say to myself

> Go ahead, do it instead of looking on the web, you have done it millions of times, it will go faster

And no. 

<!--more-->


It doesn't go faster. :) I always forget one small thing and then I lose a considerable amount of time fixing the problem. So here it is , one single place for me to look for the information.

# Configuration wrapper

{% highlight csharp linenos %}
public interface IConfigurationWrapper
{
    string SqlServerConnectionString { get; }
    ...
}
public class ConfigurationWrapper : IConfigurationWrapper
{
    public string SqlServerConnectionString
    {
        get { return ConfigurationManager.ConnectionStrings["SqlServer"].ConnectionString; }
    }
}
{% endhighlight %}

# App.config

{% highlight xml linenos %}
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <connectionStrings>
    <clear />
    <add name="SqlServer" 
         connectionString="Data Source=DBServerName;Initial Catalog=DBName; User Id=userName;Password=password;"
         providerName="System.Data.SqlClient"/>
  </connectionStrings>
</configuration>
{% endhighlight %}

Check also [The Connection Strings Reference](http://www.connectionstrings.com) , an absolute life saver.

# Why I would need the ConfigurationWrapper ?

**ConfigurationWrapper class** : 

* avoids me to put hard codes code like the following

{% highlight csharp linenos %}
ConfigurationManager.ConnectionStrings["SqlServer"].ConnectionString;
{% endhighlight %}

in my classes 

* lets me to mock the interface `IConfigurationWrapper` during unit tests. This way, I don't copy or reference `app.config` in my test classes.

# How to consume ConfigurationWrapper ?

This type can be consumed by injecting it through the constructor.

{% highlight csharp linenos %}
public class SqlServerDataImporter : IDataImporter
{
   private readonly IConfigurationWrapper _configuration;
   private readonly IOutput _output;

   public SqlServerDataImporter(IConfigurationWrapper configuration, IOutput output)
   {
       _configuration = configuration;
       _output = output;
   }
...
}

{% endhighlight %}
