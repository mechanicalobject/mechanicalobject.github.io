---
layout: post
author : Mechanical Object
category: Article
title: Working with SQLite on .NET-3
tagline: 
tags: [.net, c#, database, decompiling, sqlite, programming]
published : true
---
[In the previous article of this series]({{ site.baseurl }}{% post_url 2015-01-07-Working-with-SQLite-on-DotNet-2 %}){:target="_blank"}
, I looked into different parameters that can be defined in a SQLite connection string. Detailed 
map will serve when I'll need behaviors that the default values cannot provide. In this article, 
I will try to learn how to do CRUD operations on a SQLite database.

<!--more-->

# Data Context

Let's define a data context _-I am not talking about DataContext keyword that is used in Entity Framework-_. The database I need will hold todo data. I'll imagine that I created an application and the user interactions are defined is the following:

*   A todo entry is defined by:
    *   unique id
    *   description
    *   creation date
    *   modification date ( at the moment of creation modification date is the same as creation date)
    *   status (3 available status: to be done, postponed, done)
*   The user can add a new todo
*   The user can display all the todos or some todos by filtering on a date range
*   The user can delete a todo
*   The user can update a todo
    *   by modifying the description
    *   by changing its status

Given the information above, I can create my model class like the following

{% highlight csharp linenos %}
public class Todo
{
   public int Id {get;set;}
   public string Description {get;set;}
   public DateTime CreatedOn {get;set;}
   public DateTime ModifiedOn {get;set;}
   public TodoStatus Status {get;set;}

   public override string ToString()
   {
       return string.Format(@"Id : {0}, Description = {1}, Status = {2} CreatedOn = {3} ModifiedOn = {4}",
                Id, Description.Substring(0, 10) + "...", Status,CreatedOn, ModifiedOn);
   }
}
public enum TodoStatus
{
   ToBeDone = 0,
   Postponed = 1,
   Done = 2,
}

{% endhighlight %}


To create data access layer for my application, I'll need following methods:

{% highlight csharp linenos %}
void CreateTodoTable(){}
void AddNewTodo(Todo todoToAdd){}
List<Todo> SelectAllTodos(){}
List<Todo> SelectTodosFromRange(DateTime lowerBound, DateTime upperBound){}
void UpdateTodoDescription(Todo todoToUpdate){}
void UpdateTodoStatus(Todo todoToUpdate){}
void DeleteTodo(Todo todoToDelete){}

{% endhighlight %}


Note: You may say _-Hey, why don't you design generic methods so that you can use them in another project?_-. This would be perfectly valid proposition but let's not lose focus, the sole purpose of this series of articles is to learn how to **manipulate** SQLite.

# Creating Todo table

When you create a table , let's say in SQL Server, you have a 
[multitude of choice for column types](http://msdn.microsoft.com/en-us/library/ms187752.aspx){:target="_blank"} 
that will hold the data and 
[it is quite easy to make correspondence](http://msdn.microsoft.com/en-us/library/cc716729%28v=vs.110%29.aspx){:target="_blank"} 
between the type of a field/property of your model class and the type of the column. However SQLite 
uses a dynamic type system which consists of

*   defining storage classes
*   defining type affinity rules
*   map data types to a certain type affinity

The conception aims to guarantee backward compatibility and migration of the data to/from other 
relational database management systems. To have a deeper understanding, please visit 
[SQLite official web site](https://www.sqlite.org/datatype3.html){:target="_blank"} Let's come back 
to our subject and try to define data type mappings for our `Todo` class:

| Property | Property type | SQLite data type |
| --- | :-- | :-- |
| Id | Int32 | int |
| Description | String | nvarchar |
| CreatedOn | DateTime | datetime |
| ModifiedOn | DateTime | datetime |
| Status | Enum | int |

I sense a smell in the mapping for the Status column. Maybe in the future, I'll need to create a separate table and use id of this column in todo table to avoid hard coded conversions in the source code. Note that I simply expose my concerns about the limitations that the actual design may cause, I don't try over-engineer to be able to stay in the context and finish the exercise. By translating mapping table above to sql script, I get:

{% highlight sql linenos %}
create table Todo
(
  id int primary key, 
  description nvarchar,
  createdOn datetime default current_timestamp,
  modifiedOn datetime default current_timestamp,
  status int
);

{% endhighlight %}


Let's implement CreateTodoTable method:

{% highlight csharp linenos %}
void CreateTodoTable()
{
     const string connectionString = @"Data Source=d:\Test.db3;Version=3;";
     const string query = @"create table Todo
                         (
                         id int primary key, 
                         description nvarchar,
                         createdOn datetime default current_timestamp,
                         modifiedOn datetime default current_timestamp,
                         status int
                         )";
     using (SQLiteConnection connection = new SQLiteConnection(connectionString))
     {
         connection.Open();
         var command = connection.CreateCommand();
         command.CommandText = query;
         command.ExecuteNonQuery();
     }
}

{% endhighlight %}


Please note that this isn't an ideal way to do at all. Once I finish implementing CRUD methods, 
I will review all the code snippets to spot weak points.

# Inserting data into the database

{% highlight csharp linenos %}
{
   ...
  var todo = new Todo()
  {
    Id = 1,
    CreatedOn = new DateTime(2010, 2, 3),
    ModifiedOn = new DateTime(2014, 2, 12),
    Description = "Finish painting the house!",
    Status = TodoStatus.ToBeDone
  };
  AddNewTodo(todo);
}

void AddNewTodo(Todo todoToAdd)
{
   const string connectionString = @"Data Source=d:\Test.db3;Version=3;";
   using (SQLiteConnection connection = new SQLiteConnection(connectionString))
   {
    connection.Open();
    var query =@"INSERT INTO Todo (Id, Description, CreatedOn, ModifiedOn,Status) VALUES 
                                      (@id, @description, @createdOn, @modifiedOn,@status)";
    SQLiteCommand insertCommand = connection.CreateCommand();
    insertCommand.CommandType = CommandType.Text;
    insertCommand.CommandText = query;
    insertCommand.Parameters.AddWithValue("@id",todoToAdd.Id);
    insertCommand.Parameters.AddWithValue("@description",todoToAdd.Description);
    insertCommand.Parameters.AddWithValue("@createdOn", todoToAdd.CreatedOn);
    insertCommand.Parameters.AddWithValue("@modifiedOn", todoToAdd.ModifiedOn);
    insertCommand.Parameters.AddWithValue("@status", (int)todoToAdd.Status);
    insertCommand.ExecuteNonQuery();
   }
}
{% endhighlight %}

Let's get someresults from database.

# Selecting a result set from database

{% highlight csharp linenos %}

var allTodosInTheDatabase = SelectAllTodos();
foreach (var todo in allTodosInTheDatabase)
{
        Console.WriteLine(todo);
}
Console.Read();

...
List<Todo> SelectAllTodos()
{
    const string connectionString = @"Data Source=d:\Test.db3;Version=3;";
    var result = new List<Todo>();
    using (SQLiteConnection connection = new SQLiteConnection(connectionString))
    {
        connection.Open();
        var query = @"Select * from Todo";
        SQLiteCommand selectCommand = connection.CreateCommand();
        selectCommand.CommandText = query;
        SQLiteDataReader reader = selectCommand.ExecuteReader();
        while (reader.Read())
        {
            var todo = new Todo();
            todo.Id = (int)reader["Id"];
            todo.Description = (string) reader["description"];
            todo.CreatedOn = (DateTime) reader["createdOn"];
            todo.ModifiedOn = (DateTime) reader["modifiedOn"];
            todo.Status = (TodoStatus) Enum.Parse(typeof (TodoStatus), reader["status"].ToString());
            result.Add(todo);
        }
    }
    return result;
}

{% endhighlight %}

When executed in a console application, this code snippets will give following result

{% highlight console %}

{% raw %}
Id : 1, Description = Finish pai..., Status = ToBeDone CreatedOn = 03/02/2010 00
:00:00 ModifiedOn = 12/02/2014 00:00:00
{% endraw %}
{% endhighlight %}


# Updating the database

Now that we inserted a todo row into the database, let's change its status now:

{% highlight csharp linenos %}
void UpdateTodoStatus(Todo todoToUpdate)
{
    const string connectionString = @"Data Source=d:\Test.db3;Version=3;";
    var query = @"UPDATE Todo Set status=@status where id=@id";
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

# Deleting data from the database

The last thing to learn now to move forward is how to delete a record from the database

{% highlight csharp linenos %}
void DeleteTodo(Todo todoToDelete)
{
    const string connectionString = @"Data Source=d:\Test.db3;Version=3;";
    var query = @"DELETE From Todo where id=@id";
    using (SQLiteConnection connection = new SQLiteConnection(connectionString))
    {
        connection.Open();
        SQLiteCommand deleteCommand = connection.CreateCommand();
        deleteCommand.CommandType = CommandType.Text;
        deleteCommand.CommandText = query;
        deleteCommand.Parameters.AddWithValue("@id", todoToDelete.Id);
        deleteCommand.ExecuteNonQuery();
    }
}

{% endhighlight %}


# What is wrong with the code snippets?

Now, let's take a step back and criticize the code snippets _-which all worked fine during my tests-_:

*   The code needs to be reorganized into one class that'll separate business logic from data access logic
*   Hard coded connection string is repeated in every method
*   Error handling is missing. _-What happens if insert or update fails?-_
*   Guard clauses are missing. _-What happens if todo object that is received by insert; update or select methods is null ?-_

# What's next?

In the next article, I'll try to clean the code, group it into one reusable component
 _-do you see repository pattern smile?-_ and try to address fixes to the concerns I raised above.