---
layout: post
status: publish
published: true
comments: true
title: Simple Mini Profiler Glimpse plugin
summary: I just created my first Glimpse plugin, integration between Mininprofiler and Glimpse.
date: 2011-06-19 18:01:35 +0200
categories:
- Programming
tags:
- ASP.NET
- ASP.NET MVC
- glimpse
- profiler
- plugin
---
I recently published on Github a simple small project ([miniprofiler-glimpse-plugin](https://github.com/mcliment/miniprofiler-glimpse-plugin)) to integrate the results collected by the excellent [MiniProfiler](http://miniprofiler.com/) created by the people at Stackoverflow with [Glimpse](http://getglimpse.com/), the Firebug for ASP.NET and MVC applications.

Creating Glimpse plugins is really easy (just implement `IGlimpsePlugin` and decorate it with `GlimpsePluginAttribute`), implement the methods and reference the plug-in assembly in the project where you are using Glimpse. [MEF](http://mef.codeplex.com/) will do the gluing and the [JSON.NET](http://json.codeplex.com/) will do its best to deserialize the results in a human readable way.

To use this plug-in, just compile it and reference the assembly in your Glimpse-enabled project. I you have questions, suggestions or bug reports, you can use Github's own issue reporting system, drop me a line o fork the project and make your improvements.

So, I hope you like it.

Link: [miniprofiler-glimpse-plugin](https://github.com/mcliment/miniprofiler-glimpse-plugin) [github.org]
