---
layout: post
author : Mechanical Object
category: Article
title: Working with SQLite on .NET–5
tagline: 
tags: [.net, c#, github, programming, sqllite]
published : true
---

Here is the final article of SQLite series. 

<!--more-->

# SQLite Limits

I don't expect you to read entirely but at least take a look at 
[these notes](https://www.sqlite.org/limits.html){:target="_blank"}
It is important to know limitations of the tool you are using. This may also affect your choice regarding the 
tool you'll use in your project.



# Optimizing performances

[Thomas Bandt](http://blog.thomasbandt.de/39/2433/de/blog/performance-optimization-of-sqlite-on-ios-with-xamarin.html){:target="_blank"}
made a nice blog post and kindly gives some tips based on his experience. Also, please 
[take a look at the official web site](https://www.sqlite.org/cvstrac/wiki?p=PerformanceTuning){:target="_blank"}
which contains valuable information.

# Making SQLite calls async

There is a  [GitHub repo](https://github.com/praeclarum/sqlite-net){:target="_blank"} which'll let you to do 
async data access. It is _-for the moment-_ the best one I found.

# What if I want to use it on mobile apps?
[This fellow](http://blog.tpcware.com/2014/05/universal-app-with-sqlite-part-1/){:target="_blank"}
created a series of two articles which explain very clearly how to use SQLite on universal applications. 
Also, [this article](http://blogs.msdn.com/b/robertgreen/archive/2012/11/13/using-sqlite-in-windows-store-apps.aspx){:target="_blank"}
_-despite the fact that it is a bit old-_ gives good idea of where to start.

# What if I want to parse an existing CSV to fill my database?

That happened to me when I was working on a project that employs SQL Server :). What I did was to create a data 
importer class using a library called [fast member](https://code.google.com/p/fast-member/){:target="_blank"}
The basic idea is:

*   parse the CSV
*   transform lines into objects
*   gather these objects using a list
*   bulk insert them into the database (fast-member takes a list and creates a reader that you can 
use in in `WriteToServer` method. Here is a code snippet:


{% highlight csharp %}

private void BulkInsert(List<YourModelClass> list)
{
    using (var connection = new SqlConnection(_configuration.SqlServerConnectionString))
    {
        connection.Open();
        _output.WriteLine("connection opened with success!");
        try
        {
            using (var bcp = new SqlBulkCopy(connection))
            {
                using (var reader = ObjectReader.Create(list, 
                                                        "Property1",
                                                        "Property2", 
                                                        "Property3", 
                                                        "Property4"))
                {
                    bcp.DestinationTableName = "TableNameInDatabase";
                    bcp.WriteToServer(reader);
                }
            }
            list.Clear();
        }
        catch (SqlException exception)
        {
            _output.WriteLine(string.Format("some shxx happened {0} {1} {2}", 
                              exception, 
                              exception.Message,
                              exception.InnerException));
        }
        catch (Exception exception)
        {
            _output.WriteLine(string.Format("some shxx happened {0}", exception.Message));
        }
    }
}
{% endhighlight %}

# Are there any other tools that can facilitate my life?

Along with fast-member, there are 2 libraries that I use frequently :

* [Dapper](https://github.com/StackExchange/dapper-dot-net){:target="_blank"}
* [Automapper](https://github.com/AutoMapper/AutoMapper){:target="_blank"}
