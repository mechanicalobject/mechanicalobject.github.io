---
layout: post
author : Mechanical Object
category: Article
title: Testing DataAnnotations
tagline: 
tags: [.net, c#, data annotations, t.d.d, unit test, nunit]
published : true
---
Let's take one basic model

```csharp
public class Person : ModelBase 
{
    [Required(ErrorMessage)=typeof(ErrorStrings), ErrorMessageResourceName="Required")]
    public string FirstName{get;set;}
}

```

To be able to test **Required** attribute, at some point I will need to

<!--more-->

1.  instantiate the person class
2.  assign an invalid value to the FirstName property whic would be `null` or `String.Empty`
3.  have some validation logic in this class or in one of its base classes (_in this case ModelBase or Object_)

While looking for a good way to do that , I have find a nice helper method shared by **scorpio** [in this stackoverflow post](http://stackoverflow.com/a/2193988/1991801). If we elaborate the 3rd point above, 3 options came to my mind

1.  embed the validation logic to ModelBase class, hence all the model will take benefit of it
2.  create an extension method for ModelBase class
3.  create an extension method for Object class

I eliminated 3rd option since only my entity model classes would need the validation logic. In addition to that in my opinion, it is not a good practice to create directly this kind of "limited scoped" behaviors on `Object` class. Let's recall, **everything in .Net inherits from Object class.** So I opted for the 2nd option but I might as well as go for the 1st. Here is the result :

```csharp
internal static void Validate(this ModelBase model)
{
   var type = model.GetType();
   var meta = type.GetCustomAttributes(false).OfType<MetadataTypeAttribute>().FirstOrDefault();
   if (meta != null)
   {
       type = meta.MetadataClassType;
   }
   var propertyInfo = type.GetProperties();
   foreach (var info in propertyInfo)
   {
       var attributes = info.GetCustomAttributes(false).OfType<ValidationAttribute>();
       foreach (var attribute in attributes)
       {
           var objPropInfo = obj.GetType().GetProperty(info.Name);
           attribute.Validate(objPropInfo.GetValue(obj, null), info.Name);
        }
    }
}
```

It's important to note that `attribute.Validate(...)` statement will throw an exception in case one of the property values is not confirm with the wanted validation behaviour _in our case Required_ Ready to roll, let's write the test. The goal will be make a statement using Validate() method, make it throw an exception and test that.

```csharp
[Test]
public void FirstNameIsRequired()
{
   var person = new Person{FirstName = String.Empty};
   Assert.That(()=>person.IsValid(),
               Throws.
               Exception.
               TypeOf<ValidationException>());
}
```