---
layout: post
status: publish
published: true
comments: true
title: Testing ConfigurationSection
summary: A discussion on how a simple test on the ConfigurationSection may work but won't do it on a real environment because the way the classes are constructed
date: 2010-11-24 16:17:15 +0100
categories:
- Programming
tags:
- mvccontrib
- testing
- bugs
---
![MVC Contrib Logo](/images/logoset-final.jpg)

Creating [ConfigurationSection](http://msdn.microsoft.com/en-us/library/system.configuration.configurationsection.aspx)s can be tricky but it's quite straightforward to test them, provided you take care and don't try to do too much like this subtle bug in [MvcContrib](http://mvccontrib.codeplex.com/).IncludeHandling that lead me crazy for a couple of hours.

NOTE: I sent a pull request that was accepted and the bug is fixed in these two commits: [6c654a152d8d](https://mvccontrib.codeplex.com/SourceControl/changeset/6c654a152d8d) and [0368c6094f2b](https://mvccontrib.codeplex.com/SourceControl/changeset/0368c6094f2b).

The thing is that the code does like this:

{% highlight csharp %}
public IncludeHandlingSectionHandler()
{
    _types = new Dictionary<IncludeType, IIncludeTypeSettings>
    {
        { IncludeType.Css, Css },
        { IncludeType.Js, Js }
    };
}
{% endhighlight %}

Where this is the constructor and .Css and .Js properties are marked with [ConfigurationProperty](http://msdn.microsoft.com/en-us/library/system.configuration.configurationpropertyattribute.aspx), so in theory seems correct.

In the tests, there's an XML file with the section and it's loaded using the [DeserializeSection](http://msdn.microsoft.com/en-us/library/system.configuration.configurationsection.deserializesection.aspx) method of the ConfigurationSection base class. And this seems correct, everything goes ok and the dictionary is populated correctly with the data from the XML file.

But it's wrong. If you add the section to the `app.config` (or `web.config`) file and instead of using DeserializeSection you use [ConfigurationManager.GetSection(sectionName)](http://msdn.microsoft.com/en-us/library/system.configuration.configurationmanager.getsection.aspx), the constructor is called **before** populating the Css and Js properties so the dictionary is not created properly but as those properties return a default implementation in case there's no configuration, it seems to work, but the configured values are never loaded.

This way, tests pass but it's impossible to configure the library via `web.config`. Anyway, in my opinion the problem is in .NET Framework because internally, GetSection should use DeserializeSection to be consistent.