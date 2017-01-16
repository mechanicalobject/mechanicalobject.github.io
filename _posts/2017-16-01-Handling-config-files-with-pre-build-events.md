---
layout: post
author : Mechanical Object
category: Article
title: Handling config files with pre-build events
tagline: 
tags: [c#, build, config files, build events]
published : false
--- 

### Directories the project 

### Environments 

### Modifications in the project file 
If you have app.config in your project

{% raw %}
  <Target Name="BeforeBuild" Condition="'$(Configuration)' != 'Debug' And '$(Configuration)' != 'Release'">
    <Delete Files="$(ProjectDir)\App.config" />
    <Copy SourceFiles="$(ProjectDir)\_Configuration\App.$(Configuration).config" DestinationFiles="$(ProjectDir)\App.config" />
  </Target>
{% endraw %}

If you have web.config in your project

{% raw %}
  <Target Name="BeforeBuild" Condition="'$(Configuration)' != 'Debug' And '$(Configuration)' != 'Release'">
    <Delete Files="$(ProjectDir)\Web.config" />
    <Copy SourceFiles="$(ProjectDir)\_Configuration\Web.$(Configuration).config" DestinationFiles="$(ProjectDir)\Web.config" />
  </Target>
{% endraw %}