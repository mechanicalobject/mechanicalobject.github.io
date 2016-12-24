---
layout: post
author : Mechanical Object
category: Article
title: Working with SQLite on .NET-2
tagline: 
tags: [.net, c#, database, decompiling, sqlite, programming]
published : true
---
[In the previous article of this series]({{ site.baseurl }}{% post_url 2015-01-07-Working-with-SQLite-on-DotNet-1 %}){:target="_blank"}, 
I tried to understand creation of a SQLite database and to discover tools, libraries 
that _wrap_ SQLite for .Net development. In this part of the series, I want to get a 
comprehensive map of connection string parameters for SQLite. 

<!--more-->

Every database connection needs a connection string which contains essential information. 
The content of this essential information may vary depending on the database you are 
using for the project. For example for an SQL SERVER connection string `Server`, 
`Database Name` are essential and almost always part of the connection string. For 
SQLite however, [when you look on the web](https://www.connectionstrings.com/sqlite/){:target="_blank"}, 
you'll realize that very few parameters are used on the connection string.

# What are the parameters passed in the connection string

Let's take a very common SQLite connection string that you can find almost everywhere on 
{:target="_blank"}the web.

{% highlight csharp linenos %}

SQLiteConnection con = new SQLiteConnection("data source=TestDb.s3db;Version=3;")

{% endhighlight %}

What I call parameters are the attributes that you see in the connection string separated 
by ";". For example `Data Source` or `Version` are parameters of the connection string.
`TestDb.s3db` is the value of the `Data Source` parameter and `3` is the value of the 
`Version` parameter. **Important point to note:** I am using official DLL's provided by 
SQLite team. 

There are other libraries like [Finisar.SqLite](http://adodotnetsqlite.sourceforge.net/){:target="_blank"} 
which will provide different and/or additional parameters in the connection string. Unfortunately, 
I didn't find an exhaustive list of parameters that can be used in the connection string 
[except this one](http://www.nudoq.org/#!/Packages/System.Data.SQLite.Beta/System.Data.SQLite/SQLiteConnectionStringBuilder){:target="_blank"} 
which doesn't hold enough details. To get a complete list, I decompiled the binary and 
took a look at the `Open` method in _SQLiteConnection.cs_ file. You can also look at the 
comments present at the very beginning of the class description or look directly at the 
source code of `System.Data.SQLite.SQLiteConnectionStringBuilder` class. 

Here is a synthetic table which resumes these parameters with some additional information. All the 
information is borrowed from the comments in the class definition and deducted from the 
decompiled code. Regarding the headers of the following table:

*   **Property Name**: Name of the property that is in `System.Data.SQLite.SQLiteConnectionStringBuilder` class
*   **Display Name**: Data annotation that decorates the property. This information is used in the connection string.
*   **Description**: Describes what is the property for and defines an enumeration of valid values when possible
*   **Notes**: Contains some remarks and also default values if there is one for the property.

<table>
<thead>
<tr>
  <th>Property Name</th>
  <th align="left">Display Name</th>
  <th align="left">Description</th>
  <th align="left">Notes</th>
</tr>
</thead>
<tbody>
<tr>
  <td>DataSource</td>
  <td align="left">DataSource</td>
  <td align="left">This may be a file name, the string ":memory:", or any supported URI (starting with SQLite 3.7.7).Starting with release 1.0.86.0, in order to use more than one consecutive backslash. (e.g. for a UNC path), each of the adjoining backslash characters must be doubled (e.g. "&#92;Network\Share\test.db" would become "&#92;&#92;Network\Share\test.db").</td>
  <td align="left"><strong>There is no default value, this is a must-have parameter!</strong></td>
</tr>
<tr>
  <td>Uri</td>
  <td align="left">URI</td>
  <td align="left">An alternate to the data source property</td>
  <td align="left"><strong>null</strong> is the default value.</td>
</tr>
<tr>
  <td>FullUri</td>
  <td align="left">Full URI</td>
  <td align="left">An alternate to the data source property that uses the SQLite URI syntax.</td>
  <td align="left"><strong>null</strong> is the default value.</td>
</tr>
<tr>
  <td>Version</td>
  <td align="left">Version</td>
  <td align="left">Version of the SQLite you use</td>
  <td align="left"><strong>3</strong> is the default value for the library I am using at the moment I write this post.</td>
</tr>
<tr>
  <td>UseUTF16Encoding</td>
  <td align="left">Use UTF-16 Encoding</td>
  <td align="left"><strong>True</strong> <br /> <strong>False</strong></td>
  <td align="left"><strong>False</strong> is the default value.</td>
</tr>
<tr>
  <td>DateTimeFormat</td>
  <td align="left">DateTime Format</td>
  <td align="left"><strong>Ticks</strong>: Use the value of DateTime.Ticks. <br /> <strong>ISO8601</strong>:Use the ISO-8601 format.  Uses the "yyyy-MM-dd HH:mm:ss.FFFFFFFK" format for UTC and DateTime values and "yyyy-MM-dd HH:mm:ss.FFFFFFF" format for local DateTime values <br /> <strong>JulianDay</strong>: The interval of time in days and fractions of a day since January 1, 4713 BC <br /> <strong>UnixEpoch</strong>: The whole number of seconds since the Unix epoch (January 1, 1970). <br /> <strong>InvariantCulture</strong>: Any culture-independent string value that the .NET Framework can interpret as a valid DateTime. <br /> <strong>CurrentCulture</strong>:Any string value that the .NET Framework can interpret as a valid DateTime using the current culture.</td>
  <td align="left"><strong>ISO8601</strong> is the default value.</td>
</tr>
<tr>
  <td>DateTimeKind</td>
  <td align="left">DateTime Kind</td>
  <td align="left"><strong>Unspecified</strong>: Not specified as either UTC or local time.<br /> <strong>Utc</strong>: The time represented is UTC. <br /><strong>Local</strong>:The time represented is local time.</td>
  <td align="left"><strong>Unspecified</strong> is the default value.</td>
</tr>
<tr>
  <td>DateTimeFormatString</td>
  <td align="left">DateTime Format String</td>
  <td align="left">The exact DateTime format string to use for all formatting and parsing of all DateTime values for this connection.</td>
  <td align="left"><strong>null</strong> is the default value.</td>
</tr>
<tr>
  <td>BaseSchemaName</td>
  <td align="left">Base Schema Name</td>
  <td align="left">Some base data classes in the framework (e.g. those that build SQL queries dynamically) assume that an ADO.NET provider cannot support an alternate catalog (i.e. database) without supporting alternate schemas as well; however, SQLite does not fit into this model.  Therefore, this value is used as a placeholder and removed prior to preparing any SQL statements that may contain it.</td>
  <td align="left"><strong>sqlite_default_schema</strong> is the default value.</td>
</tr>
<tr>
  <td>BinaryGUID</td>
  <td align="left">Binary GUID</td>
  <td align="left"><strong>True</strong>: Store GUID columns in binary form <br /> <strong>False</strong>: Store GUID columns as text</td>
  <td align="left"><strong>True</strong> is the default value.</td>
</tr>
<tr>
  <td>CacheSize</td>
  <td align="left">Cache Size</td>
  <td align="left">cache size in bytes</td>
  <td align="left"><strong>2000</strong> is the default value.</td>
</tr>
<tr>
  <td>Synchronous(<em>-SyncMode-</em>)</td>
  <td align="left">Synchronous</td>
  <td align="left"><strong>Normal</strong>: Normal file flushing behavior <br /> <strong>Full</strong>: Full flushing after all writes <br /> <strong>Off</strong>:  Underlying OS flushes I/O's</td>
  <td align="left"><strong>Normal</strong> is the default value. In the comments that precede SQLiteConnection class definition, the default value is indicated as <strong>Full</strong> which should be a problem of comment update.</td>
</tr>
<tr>
  <td>PageSize</td>
  <td align="left">Page Size</td>
  <td align="left">page size in bytes</td>
  <td align="left"><strong>1024</strong> is the default value.</td>
</tr>
<tr>
  <td>Password</td>
  <td align="left">Password</td>
  <td align="left">Using this parameter requires that the CryptoAPI based codec be enabled at compile-time for both the native interop assembly and the core managed assemblies; otherwise, using this parameter may result in an exception being thrown when attempting to open the connection.</td>
  <td align="left"><strong>String.Empty</strong> is the default value.</td>
</tr>
<tr>
  <td>HexPassword</td>
  <td align="left">Hexadecimal Password</td>
  <td align="left">Must contain a sequence of zero or more hexadecimal encoded byte values without a leading "0x" prefix.  Using this parameter requires that the CryptoAPI based codec be enabled at compile-time for both the native interop assembly and the core managed assemblies; otherwise, using this parameter may result in an exception being thrown when attempting to open the connection.</td>
  <td align="left"><strong>String.Empty</strong> is the default value.</td>
</tr>
<tr>
  <td>Enlist</td>
  <td align="left">Enlist</td>
  <td align="left"><strong>Y</strong>: Automatically enlist in distributed transactions <br /> <strong>N</strong>: No automatic enlistment</td>
  <td align="left"><strong>Y</strong> is the default value</td>
</tr>
<tr>
  <td>Pooling</td>
  <td align="left">Pooling</td>
  <td align="left"><strong>True</strong>: Use connection pooling.<br /> <strong>False</strong>: Do not use connection pooling.</td>
  <td align="left"><strong>False</strong> is the default value. <br /> <strong>Warning</strong>: When using the default connection pool implementation,setting this property to True should be avoided by applications that make use of COM (either directly or indirectly) due to possible deadlocks that can occur during the finalization of some COM objects.</td>
</tr>
<tr>
  <td>FailIfMissing</td>
  <td align="left">Fail If Missing</td>
  <td align="left"><strong>True</strong>:  Don't create the database if it does not exist, throw an error instead <br /> <strong>False</strong>: Automatically create the database if it does not exist</td>
  <td align="left"><strong>False</strong> is the default value.</td>
</tr>
<tr>
  <td>MaxPageCount</td>
  <td align="left">Maximum Page Count</td>
  <td align="left">Limits the maximum number of pages (limits the size) of the database</td>
  <td align="left"><strong>0</strong> is the default value to indicate there isn't any limit.</td>
</tr>
<tr>
  <td>Legacy Format</td>
  <td align="left">Legacy Format</td>
  <td align="left"><strong>True</strong>: Use the more compatible legacy 3.x database format <br /> <strong>False</strong>: Use the newer 3.3x database format which compresses numbers more effectively</td>
  <td align="left"><strong>False</strong> is the default value.</td>
</tr>
<tr>
  <td>DefaultTimeout</td>
  <td align="left">Default Timeout</td>
  <td align="left">The default command timeout in <strong>seconds</strong></td>
  <td align="left"><strong>30</strong> is the default value</td>
</tr>
<tr>
  <td>JournalMode</td>
  <td align="left">Journal Mode</td>
  <td align="left"><strong>Delete</strong>:  Delete the journal file after a commit <br /> <strong>Persist</strong>: Zero out and leave the journal file on disk after a commit <br /> <strong>Off</strong> Disable the rollback journal entirely</td>
  <td align="left"><strong>Delete</strong> is the default value</td>
</tr>
<tr>
  <td>ReadOnly</td>
  <td align="left">Read Only</td>
  <td align="left"><strong>True</strong>: Open the database for read only access <br /> <strong>False</strong>: Open the database for normal read/write access</td>
  <td align="left"><strong>False</strong> is the default value</td>
</tr>
<tr>
  <td>Max Pool Size</td>
  <td align="left"></td>
  <td align="left">The maximum number of connections for the given connection string that can be in the connection pool</td>
  <td align="left"><strong>100</strong> is the default value</td>
</tr>
<tr>
  <td>DefaultIsolationLevel</td>
  <td align="left">Default Isolation Level</td>
  <td align="left">The default transaction isolation level. The enum <code>IsolationLevel</code> is defined in the source code as the following:  <br /> <strong>Unspecified</strong> = -1 <br /> <strong>Chaos</strong> = 16 <br /> <strong>ReadUncommitted</strong> = 256 <br /> <strong>ReadCommitted</strong> = 4096 <br /><strong>RepeatableRead</strong> = 65536 <br /> <strong>Serializable</strong> = 1048576 <br /> <strong>Snapshot</strong> = 16777216</td>
  <td align="left"><strong>Serializable</strong> is the default value. <br /> <strong>Warning</strong> I strongly advice not to play with this.</td>
</tr>
<tr>
  <td>ForeignKeys</td>
  <td align="left">Foreign Keys</td>
  <td align="left"><strong>True</strong>: Enable foreign key constraints <br /> <strong>False</strong>: Disable foreign key constraints</td>
  <td align="left"><strong>False</strong> is the default value</td>
</tr>
<tr>
  <td>Flags</td>
  <td align="left">Flags</td>
  <td align="left">Extra behavioral flags for the connection.  See below the System.Data.SQLite.SQLiteConnectionFlags enumeration for possible values.</td>
  <td align="left"><strong>System.Data.SQLite.SQLiteConnectionFlags.Default</strong> is the default value</td>
</tr>
<tr>
  <td>SetDefaults</td>
  <td align="left">Set Defaults</td>
  <td align="left"><strong>True</strong>: Apply the default connection settings to the opened database. <br /> <strong>False</strong>:Skip applying the default connection settings to the opened database.</td>
  <td align="left"><strong>True</strong> is the default value.</td>
</tr>
<tr>
  <td>ToFullPath</td>
  <td align="left">To Full Path</td>
  <td align="left"><strong>True</strong>: Attempt to expand the data source file name to a fully qualified path before opening. <br /> <strong>False</strong>: Skip attempting to expand the data source file name to a fully qualified path before opening.</td>
  <td align="left"><strong>True</strong> is the default value.</td>
</tr>
<tr>
  <td>NoSharedFlags</td>
  <td align="left">No Shared Flags</td>
  <td align="left"><strong>True</strong>:  Skip using the configured shared connection flags<br /> <strong>False</strong>: Do not skip using the configured shared connection flags</td>
  <td align="left"><strong>False</strong> is the default value.</td>
</tr>
</tbody>
</table>

# SQLiteConnectionFlags

{% highlight csharp linenos %}
[Flags]
public enum SQLiteConnectionFlags
{
    None = 0,
    LogPrepare = 1,
    LogPreBind = 2,
    LogBind = 4,
    LogCallbackException = 8,
    LogBackup = 16,
    NoExtensionFunctions = 32,
    BindUInt32AsInt64 = 64,
    BindAllAsText = 128,
    GetAllAsText = 256,
    NoLoadExtension = 512,
    NoCreateModule = 1024,
    NoBindFunctions = 2048,
    NoLogModule = 4096,
    LogModuleError = 8192,
    LogModuleException = 16384,
    TraceWarning = 32768,
    ConvertInvariantText = 65536,
    BindInvariantText = 131072,
    NoConnectionPool = 262144,
    UseConnectionPool = 524288,
    UseConnectionTypes = 1048576,
    NoGlobalTypes = 2097152,
    StickyHasRows = 4194304,
    StrictEnlistment = 8388608,
    MapIsolationLevels = 16777216,
    DetectTextAffinity = 33554432,
    DetectStringType = 67108864,
    NoConvertSettings = 134217728,
    BindAndGetAllAsText = GetAllAsText | BindAllAsText,
    ConvertAndBindInvariantText = BindInvariantText | ConvertInvariantText,
    BindAndGetAllAsInvariantText = BindAndGetAllAsText | BindInvariantText,
    ConvertAndBindAndGetAllAsInvariantText = BindAndGetAllAsInvariantText | ConvertInvariantText,
    LogAll = LogModuleException | LogModuleError | LogBackup | LogCallbackException | LogBind | LogPreBind | LogPrepare,
    Default = LogModuleException | LogCallbackException,
    DefaultAndLogAll = Default | LogModuleError | LogBackup | LogBind | LogPreBind | LogPrepare,
}

{% endhighlight %}


That's was quite lecture for me and while reading the source code, I noticed some important points.

# Display name vs parameter

When you try to create a new connection with your connection string, behind the doors, SQLite tries to parse it into a 

`SortedDictionary<string ,string>` 

using 

`SQLiteConnection.ParseConnectionString` method. This method has 2 overloads:

{% highlight csharp linenos %}
private static SortedList<string, string> ParseConnectionString(string connectionString, bool parseViaFramework)
{
  if (!parseViaFramework)
     return SQLiteConnection.ParseConnectionString(connectionString);
  else
     return SQLiteConnection.ParseConnectionStringViaFramework(connectionString, false);
}

{% endhighlight %}


The following part will explain the way following method

{% highlight csharp linenos %}
SQLiteConnection.ParseConnectionString(string connectionString)

{% endhighlight %}


works:

1.  It splits the string using ";" as separator *
2.  Itt finds the position of "=", from now on it has a left part like `Data Source` and right part like `TestDb.s3db`.
3.  For each part, it gets rid of the spaces on the left side and on the right side using of "=" using `String.Trim` method
4.  It adds the left side as key , the right side as value.
5.  It tries to find the key using the following method:

{% highlight csharp linenos %}
static internal string FindKey(SortedList<string, string> items, string key, string defValue)
{
  string ret;
  if (String.IsNullOrEmpty(key)) return defValue;
  if (items.TryGetValue(key, out ret)) return ret;
  if (items.TryGetValue(key.Replace(" ", String.Empty), out ret)) return ret;
  return defValue;
}

{% endhighlight %}


It is important to note that the lookup value is hardcoded in the `Open` method that calls `FindKey` method.

{% highlight csharp linenos %}
...
string str = SQLiteConnection.FindKey(sortedList, "Data Source", (string) null);

{% endhighlight %}


Given these steps all the followings would work _-look at how `DataSource` parameter is formatted-_.

{% highlight csharp linenos %}
SQLiteConnection con = new SQLiteConnection("data source=TestDb.s3db;Version=3;")
SQLiteConnection con = new SQLiteConnection("datasource=TestDb.s3db;Version=3;")
SQLiteConnection con = new SQLiteConnection("DataSource=TestDb.s3db;Version=3;")
SQLiteConnection con = new SQLiteConnection("Data Source=TestDb.s3db;Version=3;")
SQLiteConnection con = new SQLiteConnection("data Source=TestDb.s3db;Version=3;")
SQLiteConnection con = new SQLiteConnection("Data source=TestDb.s3db;Version=3;")

{% endhighlight %}


On the other hand the following would not work _-an additional space between data and source-_:

{% highlight csharp linenos %}
(SQLiteConnection con = new SQLiteConnection("Data  Source=TestDb.s3db;Version=3;")) // with an additional space

{% endhighlight %}


# Boolean values of the parameters

If you look carefully on the table of connection parameters above, you'll see that for boolean values "Y" , "N", "True", "False" are used. How is that managed ? Let's take an example of a property:

{% highlight csharp linenos %}
[DisplayName("To Full Path")]
[Browsable(true)]
[DefaultValue(true)]
public bool ToFullPath
{
  get
  {
    object source;
    this.TryGetValue("tofullpath", out source);
    return SQLiteConvert.ToBoolean(source);
  }
  set
  {
    this["tofullpath"] = (object) (bool) (value ? 1 : 0);
  }
}

{% endhighlight %}


The key to this conversion is right under our eyes: `SQLiteConvert.ToBoolean(source)`. This method checks whether the object we are sending is already a bool or not , if it is not, it calls another helper method.

{% highlight csharp linenos %}
public static bool ToBoolean(object source)
{
  if (source is bool)
    return (bool) source;
  else
    return SQLiteConvert.ToBoolean(SQLiteConvert.ToStringWithProvider(source, (IFormatProvider) CultureInfo.InvariantCulture));
}

{% endhighlight %}

We are close to find the secret, let's find the source code of 
`static bool ToBoolean(string source)`

{% highlight csharp linenos %}

public static bool ToBoolean(string source)
{
  if (string.Compare(source, bool.TrueString, StringComparison.OrdinalIgnoreCase) == 0)
    return true;
  if (string.Compare(source, bool.FalseString, StringComparison.OrdinalIgnoreCase) == 0)
    return false;
  switch (source.ToLower(CultureInfo.InvariantCulture))
  {
    case "yes":
    case "y":
    case "1":
    case "on":
      return true;
    case "no":
    case "n":
    case "0":
    case "off":
      return false;
    default:
      throw new ArgumentException("source");
  }
}

{% endhighlight %}


**Aha!** That's it. So all of the followings are accepted and will be understood by the system:

{% highlight csharp linenos %}
// to say false
SQLiteConnection con = new SQLiteConnection("data source  = TestDb.s3db;Version=3;FailIfMissing=false")
SQLiteConnection con = new SQLiteConnection("data source  = TestDb.s3db;Version=3;FailIfMissing=False")
SQLiteConnection con = new SQLiteConnection("data source  = TestDb.s3db;Version=3;FailIfMissing=no")
SQLiteConnection con = new SQLiteConnection("data source  = TestDb.s3db;Version=3;FailIfMissing=n")
SQLiteConnection con = new SQLiteConnection("data source  = TestDb.s3db;Version=3;FailIfMissing=0")
SQLiteConnection con = new SQLiteConnection("data source  = TestDb.s3db;Version=3;FailIfMissing=off")
// to say true
SQLiteConnection con = new SQLiteConnection("data source  = TestDb.s3db;Version=3;FailIfMissing=true")
SQLiteConnection con = new SQLiteConnection("data source  = TestDb.s3db;Version=3;FailIfMissing=True")
SQLiteConnection con = new SQLiteConnection("data source  = TestDb.s3db;Version=3;FailIfMissing=yes")
SQLiteConnection con = new SQLiteConnection("data source  = TestDb.s3db;Version=3;FailIfMissing=y")
SQLiteConnection con = new SQLiteConnection("data source  = TestDb.s3db;Version=3;FailIfMissing=1")
SQLiteConnection con = new SQLiteConnection("data source  = TestDb.s3db;Version=3;FailIfMissing=on")

{% endhighlight %}


# What's next?

I believe I gathered some essential information here. Now I know how to create a database and how to find it. I can move forward. In the next article, I'll try to learn how to do CRUD operations on the SQLite database.