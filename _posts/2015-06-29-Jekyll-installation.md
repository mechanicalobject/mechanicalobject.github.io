---
layout: post
author : Mechanical Object
category: Article
title: Jekyll installation
tagline: 
tags: [jekyll, blog, github pages, minddump, markdown]
published : true
---
The article I used to build my blog on github pages : 
[Build A Blog With Jekyll And GitHub Pages](http://www.smashingmagazine.com/2014/08/01/build-blog-jekyll-github-pages/){:target="_blank"}

## Useful commands
* ``` jekyll serve --watch ```
* ``` bundle install ``` 
* ``` jekyll serve --watch --incremental ``` 
* ``` bundle exec jekyll serve --watch --incremental ``` 
* ``` bundle update ``` 
* ``` bundle exec jekyll serve ``` 
* ``` bundle clean --force ``` 

## Useful mind dump

### Create a link that opens on another tab 
{% highlight md %}

[Title of the link](link){:target="_blank"} 

{% endhighlight %}

### Localhost link

 [http://localhost:4000/](http://localhost:4000/){:target="_blank"}

### Create links between posts 

{% highlight md %}
{% raw %}
[Name of Link]({{ site.baseurl}} {% post_url 2015-01-09-Working-with-SQLite-on-DotNet-3 %}){:target="_blank"}
{% endraw %}
{% endhighlight %}

### Highlight code blocks

{% highlight md %}
{% raw %}
{% highlight csharp linenos%}
// put your code here... 
{% endhighlight %}
{% endraw %}
{% endhighlight %}

### Display the text as it is by escaping all

{% highlight liquid %}
{% raw %}
{% raw %}
// the text that you want to display as it is goes here...
{% {% endraw %}endraw %}{% raw %}
{% endraw %}
{% endhighlight %}

For more information, you can take a look at [this markdown file.](https://raw.githubusercontent.com/SLaks/SLaks.Blog/gh-pages/_posts/2013-06-10-jekyll-endraw-in-code.md){:target="_blank"} 

### Insert an image 
{% highlight liquid %}
{% raw %}
![Coverage results with DotCover]({{ site.url }}/img/2013-04-19-Coverage-of-auto-implemented-properties-with-dotCover/dotnet-coverage-with-properties.png)
{% endraw %}
{% endhighlight %}