---
layout: post
author : Mechanical Object
category: Article
title: Working with SQLite on .NET-4
tagline: 
tags: [.net, c#, database, programming, repository pattern, sqlite, t.d.d, unit test]
published : true
---

In the [previous article of this series]({{ site.baseurl }}{% post_url 2015-01-09-Working-with-SQLite-on-DotNet-3 %}){:target="_blank"}
I learnt how to do CRUD operations on a SQLite database. I have also raised a few issues for the code snippets.
In this article, I'll try to reorganize the code and address fixes to these issues.

<!--more-->

# Issue n&deg; 0: Organizing the code

## Repository Pattern

To organize all the code I have written in [previous article]({{ site.baseurl }}{% post_url 2015-01-09-Working-with-SQLite-on-DotNet-3 %}){:target="_blank"}. 
This is a quite simple pattern that consist of regrouping all data access logic into one class named generally 
`xxxxRepository`, `xxx` being the data context in which you are. The main purpose here is to separate business 
logic from data access logic. If, in a few months, I decide to transform the application so that it works with 
LocalDb, there will be only one layer to write/modify. In the context of persisting `Todo` objects into SQLite 
database, I created the following interface with its concrete implementation named `TodoRepository`.

{% highlight csharp %}

public interface ITodoRepository
{
    void CreateTodoTable();
    void AddNewTodo(Todo todoToAdd);
    List<Todo> SelectAllTodos();
    List<Todo> SelectTodosFromRange(DateTime lowerBound, DateTime upperBound);
    void UpdateTodoDescription(Todo todoToUpdate);
    void UpdateTodoStatus(Todo todoToUpdate);
    void DeleteTodo(Todo todoToDelete);
}

{% endhighlight %}

**Note**: Whether creating a table should be part of this repository or not can be discussed.

## Configuration Wrapper

Furthermore, since I know that I'll use configuration file _-especially to address a fix to next issue-_, 
I created a `ConfigurationWrapper` class that can be injected as dependency thus I will avoid writing code 
like the below in my classes.


{% highlight csharp %}

var connectionString = ConfigurationManager.ConnectionStrings["TodoDatabase"].ConnectionString;

{% endhighlight %}

`ConfigurationWrapper` class is as simple as:

{% highlight csharp %}

public interface IConfigurationWrapper
{
    string TodoSqLiteDbConnectionString { get;  }
}

public class ConfigurationWrapper : IConfigurationWrapper
{
    public string TodoSqLiteDbConnectionString
    {
        get
        {
            return ConfigurationManager.ConnectionStrings["TodoDatabase"].ConnectionString;
        }
    }
}

... 
// how to consume ConfigurationWrapper
public class ClassThatNeedsToGetInfoFromConfigFile(IConfigurationWrapper configurationWrapper)
{
    _configurationWrapper = configurationWrapper;
}
{% endhighlight %}

## Database Connection Factory

I know there are out there people who won't agree with me because of the indirection level _- this is 
synonym to difficulty for some fellow -_ which this class will add. So why did I add it? The answer is: 
**To be able to test repository class.** Let's look at the following code:

{% highlight csharp %}
void CreateTodoTable()
{     
     const string query = "...."; // query goes here
     try
     {
         using (SQLiteConnection connection = new SQLiteConnection(connectionString))
         {
             connection.Open();
             var command = connection.CreateCommand();
             command.CommandText = query;
             command.ExecuteNonQuery();
         }
     }
     catch(SQLiteException exception)
     { 
        var message = ... // build here the message to log
        _logger.LogError(message);
     }
}    
{% endhighlight %}

How can you test the case: **"When there is a problem with the connection, log the error message"** ? 
With the code above, you cannot because of the `new SQLiteConnection(connectionString)` . That's why 
I added DatabaseConnectionFactory which generates SQL connection.

{% highlight csharp %}
public interface IDatabaseConnectionFactory
{
    IDbConnection GetNewSqlLiteConnection(string connectionString);
}

public class DatabaseConnectionFactory : IDatabaseConnectionFactory
{
    public IDbConnection GetNewSqlLiteConnection(string connectionString)
    {
        return new SQLiteConnection(connectionString);
    }
}
{% endhighlight %}

This one along with the others are injected into `TodoRepository` class:

{% highlight csharp %}
public TodoRepository(IDatabaseConnectionFactory databaseConnectionFactory,
					  IConfigurationWrapper configurationWrapper, 
					  ILogger logger)
{
    _databaseConnectionFactory = databaseConnectionFactory;
    _configurationWrapper = configurationWrapper;
    _logger = logger;
}
{% endhighlight %}

And the below test lets me verify the behavior of the method in case there is a problem while opening 
the connection:

{% highlight csharp %}
[Test]
public void CreateTodoTable_SystemShouldLogIfOpeningConnectionFails()
{
    // ==> Arrange 

    // fake the fail while opening connection
    _sqLiteConnectionMock.Setup(m=>m.Open())
                         .Throws<SQLiteException>();
    _configurationWrapperMock.Setup(m => m.TodoSqLiteDbConnectionString)
                             .Returns(ConnectionString);
    _databaseConnectionFactoryMock.Setup(m => m.GetNewSqlLiteConnection(ConnectionString))
                                  .Returns(_sqLiteConnectionMock.Object);

    // get concrete objects
    var databaseConnectionFactory = _databaseConnectionFactoryMock.Object;
    var configurationWrapper = _configurationWrapperMock.Object;
    var logger = _loggerMock.Object;

    // create the system under test
    var sut = new TodoRepository(databaseConnectionFactory, configurationWrapper, logger);

    // ==> Act
    sut.CreateTodoTable();

    // ==> Assert
    _loggerMock.Verify(m=>m.WriteLine(It.IsAny<string>()),Times.Once());
}
{% endhighlight %}

This code snippet gives also a clue regarding **the issue n&deg;2: error handling** which you'll 
read in a minute.

# Issue n&deg; 1: Hard coded connection strings

[In the previous article]({{ site.baseurl }}{% post_url 2015-01-09-Working-with-SQLite-on-DotNet-3 %}){:target="_blank"}, 
there were hard coded connection strings in every method:

{% highlight csharp %}
var connectionString = @"Data Source=test1.sql3db";
{% endhighlight %}
To see the problem, I have asked myself "What happens if I change the connection 
string": 
1\. I'll need to change it in every method ⇒ code duplication 
2\. I'll have to **re-compile** the code. In production environments it means deployment. That is not 
something you would want for a simple change as connection string. 
The solution is to add an `app.config` in the solution and place the connection string into 
`<connectionstrings></connectionstrings>` section. Right click to the solution, add new item, search 
for `config` and choose `Application Configuration File` 
After having inserted `<connectionstrings></connectionstrings>` section, app.config looks like:

{% highlight xml %}
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <startup>
    <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
  </startup>
  <connectionStrings>
    <clear/>
    <add name="TodoDatabase"
         connectionString="data source=|DataDirectory|Todo.db3"
         providerName="System.Data.SqlLite"/>
  </connectionStrings>
</configuration>
{% endhighlight %}

Please note the usage of `|DataDirectory|`. 
From [MSDN:](http://msdn.microsoft.com/en-us/library/cc716756.aspx){:target="_blank"}

> Resolves to a relative path to a mapping and metadata files. This is the value that is set through the 
AppDomain.SetData("DataDirectory", objValue) method. The DataDirectory substitution string must be 
surrounded by the pipe characters and there cannot be any whitespace between its name and the pipe 
characters. The DataDirectory name is not case-sensitive. If a physical directory named "DataDirectory" 
has to be passed as a member of the list of metadata paths, add whitespace should on either side or 
both sides of the name, for example: 

{% raw %}
Metadata="DataDirectory1 | DataDirectory | DataDirectory2". 
{% endraw %}

An ASP.NET application resolves |DataDirectory| to the "<application root="">/app_data" folder.</application>

# Issue n&deg; 2 : Error handling

[In the previous article]({{ site.baseurl }}{% post_url 2015-01-09-Working-with-SQLite-on-DotNet-3 %}){:target="_blank"}, 
I have written methods that realize CRUD operations but errors weren't handled properly. To refresh memories:

{% highlight csharp %}
void CreateTodoTable()
{     
     const string query = "...."; // query goes here
     using (SQLiteConnection connection = new SQLiteConnection(connectionString))
     {
         connection.Open();
         var command = connection.CreateCommand();
         command.CommandText = query;
         command.ExecuteNonQuery();
     }
}
{% endhighlight %}

The method above has a direct dependence to `SQLiteConnection`. It opens the connection, it creates a 
command and it executes this command. What happens if one of these operations fails? The code above is 
unable to take proper actions _-like, if something fails, log an error-_ I can think of 2 approaches:

1.  Wrap every code blocks that deal with SQLiteConnection with `try catch`
2.  Let the exception climb up to the top level in the code where **it should be handled**. The downside 
to that is that at the top level, probably there will be a catch using a generic exception.

I have gone with the first one and below the result:

{% highlight csharp %}
public void CreateTodoTable()
{
    const string query = "..." // query goes here
    try
    {
        var connectionString = _configurationWrapper.TodoSqLiteDbConnectionString;
        using (var connection =_databaseConnectionFactory.GetNewSqlLiteConnection(connectionString))
        {
            connection.Open();
            var command = connection.CreateCommand();
            command.CommandText = query;
            command.ExecuteNonQuery();
        }
    }
    catch (SQLiteException exception)
    {
        _logger.WriteLine(exception.Message);
        throw;
    }
}
{% endhighlight %}

**Note:** Why did I use `throw` instead of `throw exception` or `throw new Exception(...)` ?

# Issue n&deg; 3: Missing guard clauses

The final issue I raised on [previous article]({{ site.baseurl }}{% post_url 2015-01-09-Working-with-SQLite-on-DotNet-3 %}){:target="_blank"} 
was about missing guard clauses. Let's look at the following method as it was coded:

{% highlight csharp %}
void UpdateTodoStatus(Todo todoToUpdate)
{
    var query = "..." // query goes here 
    using (SQLiteConnection connection = new SQLiteConnection(connectionString))
    {
        connection.Open();
        SQLiteCommand updateCommand= connection.CreateCommand();
        updateCommand.CommandType = CommandType.Text;
        updateCommand.CommandText = query;
        updateCommand.Parameters.AddWithValue("@status", (int)todoToUpdate.Status);
        updateCommand.Parameters.AddWithValue("@id", todoToUpdate.Id);
        updateCommand.ExecuteNonQuery();
    }
}
{% endhighlight %}

What happens if I execute this code block?

{% highlight csharp %}
Todo todo = null;
UpdateStatus(todo);
{% endhighlight %}

The moment execution flow hits the line

{% highlight csharp %}
updateCommand.Parameters.AddWithValue("@status", (int)todoToUpdate.Status);
{% endhighlight %}

the code will throw a `NullReferenceException` . In this particular case, I may have two choices:

1.  Adding a guard clause in the beginning of the method to check if `todo` received as argument is null or not.
2.  Add an additional catch block for `NullReferenceException`.

In my opinion, the advantage of the first is that you can define the direction you want the code to take. 
If the argument expected by the method is null, isn't it better to throw `ArgumentNullException` instead 
of the underlying exception which gives a more mysterious message? Here is the redesigned code:

{% highlight csharp %}
public void UpdateTodoStatus(Todo todoToUpdate)
{
    const string query = @"UPDATE Todo Set status=@status where id=@id";
    try
    {
        var connectionString = _configurationWrapper.TodoSqLiteDbConnectionString;
        if (todoToUpdate == null)
            throw new ArgumentNullException("todoToUpdate",
                "Cannot update the database with a null object!");
        using (var connection = _databaseConnectionFactory.GetNewSqlLiteConnection(connectionString))
        {
            connection.Open();
            var command = connection.CreateCommand();
            command.CommandType = CommandType.Text;
            command.CommandText = query;

            var statusParameter = command.CreateParameter();
            statusParameter.ParameterName = "@status";
            statusParameter.Value = (int) todoToUpdate.Status;

            var idParameter = command.CreateParameter();
            idParameter.ParameterName = "@id";
            idParameter.Value = todoToUpdate.Id;

            command.Parameters.Add(statusParameter);
            command.Parameters.Add(idParameter);
            command.ExecuteNonQuery();
        }
    }
    catch (SQLiteException exception)
    {
        _logger.WriteLine(exception.Message);
        throw;
    }
    catch (ArgumentNullException exception)
    {
        _logger.WriteLine(exception.Message);
        throw;
    }
}
{% endhighlight %}

Have you noticed that instead of using `updateCommand.Parameters.AddWithValue(...,...)` method I am 
now using the following block ?:

{% highlight csharp %}
var statusParameter = command.CreateParameter();
statusParameter.ParameterName = "@status";
statusParameter.Value = (int) todoToUpdate.Status;
{% endhighlight %}

That's is because `AddWithValue` is in the default SQLite .Net library I installed via nuget in the 
first article. I transformed type of the `command` variable from `SQLiteCommand` to `IDbCommand` to 
break the dependency of the code with SQLite library. What happens if I plug SQL server to the 
Repository class? Would it work _- except CreateTodoTable() method -_? :) That has been said, 
I also created an extension method that'll gather 3 lines per parameter in to one method to have 
a more readable code :

{% highlight csharp %}
public static class DbCommandExtensions
{
    public static void AddParameterWithValue(this IDbCommand command, string parameterName, object value)
    {
        var parameter = command.CreateParameter();
        parameter.ParameterName = parameterName;
        parameter.Value = value;
        command.Parameters.Add(parameter);
    }
}
[TestFixture]
public class DbCommandExtensionsTest
{
    [Test]
    public void TestAddParameterWithValue()
    {
        IDbCommand command = new SQLiteCommand();
        Assert.AreEqual(0, command.Parameters.Count);
        command.AddParameterWithValue("@id", 1);
        Assert.AreEqual(1,command.Parameters.Count);

        Assert.AreEqual(1, ((SQLiteParameter)command.Parameters[0]).Value);
        Assert.AreEqual("@id", ((SQLiteParameter)command.Parameters[0]).ParameterName);
    }
}
{% endhighlight %}

## Code Samples

Code samples are available on 
[GitHub]( https://github.com/mechanicalobject/sandbox/tree/master/MechanicalObject.Sandbox.TodoWithSQLite){:target="_blank"}

## What is next?

In the next _- and final -_ article, I'll share some aspects and important links.