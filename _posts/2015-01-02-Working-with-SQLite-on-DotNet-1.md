---
layout: post
author : Mechanical Object
category: Article
title: Working with SQLite on .NET-1
tagline: 
tags: [.net, c#, database, decompiling, sqlite, programming]
published : true
---
On several projects I have used different relational databases like Oracle, Sql Server, 
MySql even Sybase _-what an horrible experience-_. I have never used SQLite or LocalDb _
-this one replace Sql Compact-_.

For the universal app I am developing, one of the constraints is that the application 
should work offline. I have choices between working with text files and using a 
lightweight databasethat I can embed into my solution. I decided to go with the SQLite 
and to discover this little beast, I created a dummy console application.

<!--more-->

# Packages I installed

I installed following packages via Nuget 

* `System.Data.SQLite Core (X86/x64) 1.0.94`
* `System.Data.SQLite Core (X86/x64) 1.0.94.1`

All the following analysis is based on this version. The API can change in the future 
versions.

# How to create a database?

There isn't any database server _-an external software to install-_. 
**The database is only a file** that you can create via an external tool or using 
the A.P.I I indicated above in Packages section. Depending on the way you create, 
the location of this file will be different. We'll have more details in the next 
section. While trying to learn how to use SQLite, I found 3 different ways to 
create a database

## 1 . Create a database using an external tool

The tools that got my attention on a simple google search are the followings:

1.  [SQLite manager (firefox plugin can be quite handy)](https://addons.mozilla.org/en-US/firefox/addon/sqlite-manager/){:target="_blank"}
2.  [SQLite browser](http://sourceforge.net/projects/sqlitebrowser/){:target="_blank"}
3.  [SQL Server Compact & SQLite Toolbox](http://sqlcetoolbox.codeplex.com/){:target="_blank"}

The third is also a Visual Studio extension. I privileged this one because it is a compact 
tool that I can use within my development environment. It can be easily installed :

{% raw %}

Tools ⇒ Extension and Updates ⇒ Search for SQL Server Compact & SQLite Toolbox

{% endraw %}


Once this extension is installed right click on **Data Connections** and chose 
**Add SqLite Connection**. The menu will guide you for the rest.

[![Sql Server compact-Lite tools](http://blog.mechanicalobject.com/wp-content/uploads/2015/01/Sql-Server-compact-Lite-tools.png)](http://blog.mechanicalobject.com/wp-content/uploads/2015/01/Sql-Server-compact-Lite-tools.png)

## 2 . Create a database using SQLiteConnection.CreateFile method

{% highlight csharp linenos %}
class Program
{
    static void Main(string[] args)
    {
    ...
    SQLiteConnection.CreateFile(nameOfTheDatabase);
    }
}
{% endhighlight %}


To insist on the fact that the database is really nothing but a file 
_-as the name of the method suggests, no server behind, you have to get used to 
it-_, here is how above method is implemented:

{% highlight csharp linenos %}
 /// <summary>
 /// Creates a database file.  This just creates a zero-byte file which SQLite
 ///             will turn into a database when the file is opened properly.
 /// 
 /// </summary>
 /// <param name="databaseFileName">The file to create</param>
 public static void CreateFile(string databaseFileName)
 {
   File.Create(databaseFileName).Close();
 }
{% endhighlight %}


## 3\. Create a database by opening a SQLite connection

This can be the most _disturbing_ part. Using SQLite, you can create a database by creating a connection and opening that connection. This is something that we can probably not imagine on _real_ database systems we have worked on.

{% highlight csharp linenos %}
class Program
{
    static void Main(string[] args)
    {
        ...
        var connectionString = @"Data Source=test1.sql3db";
        // at this point note that the following instruction DOES NOT create any 
        // SQLite database
        var connection = new SQLiteConnection(connectionString);
        // the following will create a database if there isn't any with the same name
        connection.Open();
    }
}
{% endhighlight %}


Previously, **I insisted on the fact that the SQLite database is in fact just a file**. 
When you get away from the idea of the database and you think it as a file, creating 
a file when you are _reading it/writing to it_ isn't disturbing anymore is it? If we 
recall, in .Net we can create a file while writing to it, if it doesn't exist. 
Do you see the similarity?

{% highlight csharp linenos %}
using(var fs = File.Open(path, FileMode.OpenOrCreate, FileAccess.ReadWrite))
{
    // use fs to do some task 
}
{% endhighlight %}


# Where is the database?

If you create your SQLite database using a tool (point 1 above), the database will be 
created in whatever folder you select. If you create your SQLite database using the API 
however, there are a few subtle points. Let's create our database:

{% highlight csharp linenos %}
// creating a database using CreateFile Method
SQLiteConnection.CreateFile(@"TestDb1.s3db");

// OR

// creating a database by opening a connection
SQLiteConnection con = new SQLiteConnection("Data Source=TestDb2.s3db;Version=3;");
con.Open();
...
con.Close();
{% endhighlight %}


When the statement above is executed a file named `TestDb.s3db` is created 
**in the execution folder.** **Example** Imagine you executed the program in 
debug mode. You'll find `TestDb.s3db` in:

{% raw %}

path_to_your_project-->bin-->Debug folder.

{% endraw %}


It is also valid to determine a complete path instead of only database name. This way you 
can control the location in which SQLite database will be created. The following code 
snippet will create SQLite databases on `D:\` and not on the execution folder.

{% highlight csharp linenos %}
// creating a database using CreateFile Method
SQLiteConnection.CreateFile(@"d:\TestDb1.sql3db");

// OR

// creating a database by opening a connection
SQLiteConnection con = new SQLiteConnection(@"Data Source=d:\TestDb2.s3db;Version=3;");
con.Open();
...
con.Close();
{% endhighlight %}


Have you noticed `@` symbol which makes connection string a **verbatim string literal**? 
What happens if you remove it? Would the code still compile? :) Depending on the target 
environment _-windows 8 app, windows phone 8.1 app, wpf client app etc. -_ you might want 
to have different locations. I'll dig this point later on another article.

## What's next?

I really want to jump to the part where we do some concrete operation by inserting, 
updating data and making select queries to see the result set. But there is another 
important point that deserves a deeper understanding: **SQLite connection string**. 
The samples you see on the web contain generally same parameters. I already used two 
of them, `Data Source` and `Version` in the code snippets above. There are much more 
and in the next post, I'll try to push the analysis by decompiling library code to get 
a complete map of all the parameters.