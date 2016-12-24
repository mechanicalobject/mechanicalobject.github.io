---
layout: post
author : Mechanical Object
category: Article
title: NCrunch fails to build Universal App
tagline: 
tags: [build, compilation, ncrunch, visual studio 2013]
published : true
---
Sometimes tools that I am using fail and I don't want to dig further _- not that I am lazy, simple question of time-_. 
Then I open a new tab on the navigator and start to write my search query with good dose of despair because I have 
doubts on the fact that anyone wrote about that problem before. 

<!--more-->

On my universal app, compilation was ok on visual studio but Ncrunch wasn't able to compile it. Strange. 
I have a billion other things to do , really the timing of this compilation error was chaotic. 
I searched and I found 
[a comment](http://forum.ncrunch.net/yaf_postst1241_NCrunch-Failing-to-comple--Windows-and--WindowsPhone-projects-inside-Universal-Application-solution.aspx){:target="_blank"}

> This problem seems to be related to how the template shares the App.xaml file between projects in the solution.
> Visual Studio has slightly different build logic compared to MSBuild when processing a solution as a whole, 
> so I can only assume the difference is >>allowing it to work artificially. This is probably how the issue 
managed to progress as far as it did (the RC build).

>The problem seems to be caused in line 441 of Microsoft.Windows.UI.Xaml.Common.Targets, where the XAML 
source/destination aren't taking into account the absolute file path being specified for App.xaml:

{% highlight xml %}

<GeneratedXamlSrc0 Condition="'%(AllProjectXamlPages.Link)'==''" 
                   Include="@(AllProjectXamlPages->'$(XamlGeneratedOutputPath)%(Identity)')" 
/>

{% endhighlight %}

>App.xaml has its file path specified differently because it is a solution-level item 

{% highlight xml linenos %}

("$(MSBuildThisFileDirectory)App.xaml")

{% endhighlight %}

>The easiest way to solve this problem is to specify a link attribute for the App.xaml file inside the 
HubApp.Shared.projitems build target file, in the HubApp.Shared directory. This will override the faulty 
logic and specify a relative path that works correctly with the XAML copying build step. Inside 
the .projitems file you'll find the following code:

{% highlight xml linenos %}

<ApplicationDefinition Include="$(MSBuildThisFileDirectory)App.xaml">
    <SubType>Designer</SubType>
</ApplicationDefinition>

{% endhighlight %}

>Replace this with:

{% highlight xml linenos %}

<ApplicationDefinition Include="$(MSBuildThisFileDirectory)App.xaml">
    <SubType>Designer</SubType>
    <Link>App.xaml</Link>
</ApplicationDefinition>

{% endhighlight %}

>Then reset the NCrunch engine. The projects should now build correctly.

That's it! You saved my day, thanks!