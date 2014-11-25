---
layout: post
status: publish
published: true
comments: true
title: ! 'NHibernate: Custom type is not serializable'
summary: This is an error I encountered with custom types and may help some people looking for the solution
date: 2010-06-10 18:02:33 +0200
categories:
- Programming
tags:
- NHibernate
---
Since I acquired a [NHibernateProfiler](http://www.hibernatingrhinos.com/products/NHProf) license, an initialization warning message draw my attention, the text seemed quite easy to solve:

    WARN: custom type is not Serializable: MyLibrary.MyCustomDataType

My first solution was adding `[Serializable]` attribute to the custom type, but that didn't make the trick. As it was only a warning, I did not investigate further at that moment.

![NHibernate Logo](/images/nhibernate-logo.png)

But the other day, debugging data access code I saw the warning again and the easiest solution was going to the source NHibernate code to find this:

{% highlight csharp %}
TypeFactory.InjectParameters(userType, parameters);
if (!userType.ReturnedClass.IsSerializable)
{
    LogManager.GetLogger(typeof(CustomType)).Warn("custom type is not Serializable: " + userTypeClass);
}
{% endhighlight %}

As seen in this code, the message is unclear, because the class that must be serializable is the `ReturnedClass`, not the custom `UserType` itself.

So, to solve the warning I just needed to add `[Serializable]` to the class returned by my custom `UserType` instead than the type itself.