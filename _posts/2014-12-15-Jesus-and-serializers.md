---
layout: post
author : Mechanical Object
category: Article
title: Jesus and serializers
tagline: 
tags: [.net, c#, exception, json, programming, serializer, xml]
published : true
---
[A colleague of mine](http://jounad.developpez.com/){:target="_blank"} shared with me an 
interesting case on `DataContractJsonSerializer` and `DataContractSerializer`. The 
difference between these two classes can be resumed roughly as **"one works with JSON and the other one with XML".** 

<!--more-->

Brief description from MSDN :

| Class name | Description |
| :-- | :-- |
| [DataContractJsonSerializer](http://msdn.microsoft.com/en-us/library/system.runtime.serialization.json.datacontractjsonserializer(v=vs.110).aspx){:target="_blank"} | Serializes objects to the JavaScript Object Notation (JSON) and deserializes JSON data to objects. This class cannot be inherited. |
| [DataContractSerializer](http://msdn.microsoft.com/en-us/library/system.runtime.serialization.datacontractserializer%28v=vs.110%29.aspx){:target="_blank"} | Serializes and deserializes an instance of a type into an XML stream or document using a supplied data contract. This class cannot be inherited. |

The case consists of serializing and deserializing a model _-which has a `DateTime` field-_ using these two serializers and a memorystream. First let's create a very simple model class:

# Model class

{% highlight csharp %}

public class Person
{
    public string Name { get; set; }
    public DateTime Birthday { get; set; }

    public override string ToString()
    {
        return string.Format("{0} was born on {1}", Name, Birthday.ToString("yyyy-MM-dd hh:mm:ss"));
    }
}
{% endhighlight %}


We'll also need `Serialize` and `Deserialize` methods that are able to handle the model I defined above:

# Serialize & Deserialize methods

{% highlight csharp %}

static void Serialize(XmlObjectSerializer serializer, MemoryStream memoryStream, Person personToSerialize)
{
    serializer.WriteObject(memoryStream, personToSerialize);
    memoryStream.Position = 0;
}

static Person Deserialize(XmlObjectSerializer serializer, MemoryStream memoryStream)
{
    var deserializedPerson = serializer.ReadObject(memoryStream) as Person;
    return deserializedPerson;
}
{% endhighlight %}


Please note the **type** of `serializer` parameter in both of the methods. `XmlObjectSerializer` is the base class for both `DataContractJsonSerializer` and `DataContractSerializer` and they are defined like the following :

{% highlight csharp %}

public sealed class DataContractJsonSerializer : XmlObjectSerializer {}
public sealed class DataContractSerializer : XmlObjectSerializer {}
public abstract class XmlObjectSerializer
{
    public abstract void WriteStartObject(XmlDictionaryWriter writer, object graph);
    public abstract void WriteObjectContent(XmlDictionaryWriter writer, object graph);
    public abstract void WriteEndObject(XmlDictionaryWriter writer);
    ......
}
{% endhighlight %}


# Whole picture

When we put it all together

{% highlight csharp %}

static void Main(string[] args)
{
    var serializer = new DataContractSerializer(typeof(Person));
    var jesus = new Person { Name = "Jesus", Birthday = new DateTime(1, 1, 1, 0, 0, 0) };

    Person deserializedJesus = null;
    using (var memoryStream = new MemoryStream())
    {
        Serialize(serializer, memoryStream, jesus);
        deserializedJesus = Deserialize(serializer, memoryStream);
    }
    Console.WriteLine(deserializedJesus);
}
{% endhighlight %}


and run the program. We get the following output :

{% highlight console %}
Jesus was born on 0001-01-01 12:00:00
Press any key to continue . . .
{% endhighlight %}

That is fine, let's change now the serializer from `DataContractSerializer` to `DataContractJsonSerializer` by replacing the following line

{% highlight csharp %}

var serializer = new DataContractSerializer(typeof(Person));
{% endhighlight %}

by

{% highlight csharp %}

var serializer = new DataContractJsonSerializer(typeof(Person));
{% endhighlight %}

We re-run the program and **surprise!**

{% highlight console %}

Unhandled Exception: System.Runtime.Serialization.SerializationException: DateTime values that are greater than DateTime.MaxValue or smaller than DateTime.MinValue when converted to UTC cannot be serialized to JSON. ---> System.ArgumentOutOfRangeException: Specified argument was out of the range of valid values.
Parameter name: value
   --- End of inner exception stack trace ---
   at System.Runtime.Serialization.Json.JsonWriterDelegator.WriteDateTimeInDefaultFormat(DateTime value)
   at System.Runtime.Serialization.Json.JsonWriterDelegator.WriteDateTime(DateTime value)
   at WritePersonToJson(XmlWriterDelegator , Object , XmlObjectSerializerWriteContextComplexJson , ClassDataContract , XmlDictionaryString[] )
   at System.Runtime.Serialization.Json.JsonClassDataContract.WriteJsonValueCore(XmlWriterDelegator jsonWriter, Object obj, XmlObjectSerializerWriteContextComplexJson context, RuntimeTypeHandle declaredTypeHandle)
{% endhighlight %}


**Visibly `DataContractJsonSerializer` doesn't like Jesus** :) .

# What happened here?

The error message is quite clear despite the fact that it is strangely formulated:

{% highlight console %}
DateTime values that are greater than DateTime.MaxValue or smaller than DateTime.MinValue 
when converted to UTC cannot be serialized to JSON.
{% endhighlight %}

At one point, `DataContractJsonSerializer` takes the `DateTime` value _-Jesus' birthday-_ we affected 
and it tries to convert this one to UTC and the result is not within **[DateTime.Min,DateTime.Max]**. 
Let's gather all the information we have :

*   The time zone I am in is UTC + 1\.

![Local time to utc](http://i.imgur.com/sOCDrCg.png)

*   [DateTime.Min](http://msdn.microsoft.com/en-us/library/system.datetime.minvalue%28v=vs.110%29.aspx){:target="_blank"} = 00:00:00.0000000, January 1, 0001.
*   [DateTime.Max](http://msdn.microsoft.com/en-us/library/system.datetime.maxvalue%28v=vs.110%29.aspx){:target="_blank"} = 23:59:59.9999999, December 31, 9999

**So the code does the equivalent of `new DateTime(1, 1, 1, 0, 0, 0).Add(-1)`** 
**which is lower than `DateTime.Min` constant value hence the reason of the**
**exception we got.**

# How to fix it ?

The fix would be to directly assign UTC value to Jesus' birthday by replacing the 
following line

{% highlight csharp %}

Birthday = new DateTime(1, 1, 1, 0, 0, 0)

{% endhighlight %}

with

{% highlight csharp %}

Birthday = DateTime.SpecifyKind(new DateTime(1, 1, 1, 0, 0, 0), DateTimeKind.Utc)

{% endhighlight %}

And when we run the program, we get :

{% highlight console %}

Jesus was born on 0001-01-01 12:00:00
Press any key to continue . . .

{% endhighlight %}

# Why this bug is a sneaky ?

Because depending on the time zone you are in, you may or not encounter the bug. 
If you are in UTC+0, the program will work just fine for you with both of the 
serializers.

![World time zone map](http://i.imgur.com/i2IojXsl.png)


# Important notes

1.  The source code for this article is available on [GitHub](https://github.com/mechanicalobject/sandbox/tree/master/MechanicalObject.Sandbox.DataSerializer){:target="_blank"}
2.  **Do not use** the code snippets given in this article in production code. You might want to add guard clauses against null or unwanted values and add unit tests with edge cases _- we worked on lower bound (`DateTime.Min`), what happens on upper bound (`DateTime.Max`)? -_ .

3.  [All credits for the time zone map belongs to wikipedia user TimeZonesBoy](http://en.wikipedia.org/wiki/User:TimeZonesBoy){:target="_blank"}. [The map itself is borrowed from wikipedia](http://en.wikipedia.org/wiki/Time_zone){:target="_blank"}