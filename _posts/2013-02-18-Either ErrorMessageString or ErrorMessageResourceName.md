---
layout: post
author : Mechanical Object
category: Article
title: Either ErrorMessageString or ErrorMessageResourceName must be set, but not both…
tagline: 
tags: [.net, bug, c#, programming, t.d.d, unit test]
published : true
---
I was coding calmly this evening, classical cycle : 

* test => fail to build 
* code so that it builds 
* fail the test => code so that it passes the test 

Here is my model :

<!--more-->

```csharp
public class BlogPostComment : ModelBase
{
    [Required(ErrorMessageResourceType = typeof(ErrorStrings), ErrorMessageResourceName = "Required")]
    public string CommenterName { get; set; }

    [Required(ErrorMessageResourceType = typeof(ErrorStrings), ErrorMessageResourceName = "Required")]
    [EmailAddress(ErrorMessageResourceType = typeof(ErrorStrings), ErrorMessageResourceName = "InvalidEmail")]
    [DataType(DataType.EmailAddress)]
    public string CommenterEmail { get; set; }

    public string CommenterCountry { get; set; }

    public string CommenterWebSite { get; set; }

    [Required(ErrorMessageResourceType = typeof(ErrorStrings), ErrorMessageResourceName = "Required")]
    public string Content { get; set; }

    [ForeignKey("BlogPost")]
    public long PostId { get; set; }

    public virtual BlogPost BlogPost {get;set;}
}
```

I wrote two simple unit tests to test the validation logic in my model

```csharp
[Test]
[ExpectedException(typeof(ValidationException), ExpectedMessage="The field [Email] is required!")]
public void EmailIsRequired()
{
    var blogPostComment = new BlogPostComment{CommenterEmail =string.Empty,Content ="testing", CommenterName ="Mechanical"};
    Asert.IsTrue(blogPostComment.IsValid());
}

[Test]
[ExpectedException(typeof(ValidationException), ExpectedMessage="The field [Email] is invalid!")]
public void TestInvalidEmail()
{
    var blogPostComment = new BlogPostComment{CommenterEmail ="mechanical",Content ="testing", CommenterName ="Mechanical"};
    Asert.IsTrue(blogPostComment.IsValid());
}
```

That seemed fair to me, I hit the build which was green and re-run the tests and **surprise** 

![alt text](http://blog.mechanicalobject.com/wp-content/uploads/2014/12/Test_Email_IsValid_Failed.jpg "Unit test fails with ValidationException message")

After a quick search, [I found it](https://connect.microsoft.com/VisualStudio/feedback/details/757298/emailaddress-attribute-is-unable-to-load-error-message-from-resource-mvc) :

> Posted by Microsoft on 9/18/2012 at 11:02 AM
> 
> > This is a known issue with the new data annotation attributes added in .NET 4.5\. To workaround it, explicitly set the ErrorMessage >>property of the attribute to null

After having modified the code,

```csharp
{
    ....
    [Required(ErrorMessageResourceType = typeof(ErrorStrings), ErrorMessageResourceName = "Required", ErrorMessage = null)]
    [EmailAddress(ErrorMessageResourceType = typeof(ErrorStrings), ErrorMessageResourceName = "InvalidEmail", ErrorMessage = null)]
    [DataType(DataType.EmailAddress)]
    public string CommenterEmail { get; set; }
    ...
}
```

All was in order.