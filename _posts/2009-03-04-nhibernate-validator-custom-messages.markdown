---
layout: post
status: publish
published: true
title: NHibernate Validator custom messages
summary: The Validator comes with a set of default messages but you may need to override them using your own resources
date: 2009-03-04 16:54:55 +0100
categories:
- Programming
tags:
- .NET
- i18n
- NHibernate
- nhibernate validator
---
After an [interesting thread in nhusers](http://groups.google.com/group/nhusers/browse_thread/thread/2b9f29d75c52dd89?hl=en), I wrote this is a small post explaining how you can create a simple interpolator for [NHibernate Validator](http://nhforge.org/wikis/validator/nhibernate-validator-1-0-0-documentation.aspx) that replaces the default messages for your customized ones and as well treats messages for your custom validators. Everything with internationalization using resources.

The first part is to create your own interpolator:

{% highlight csharp %}
using System.Globalization;
using System.Reflection;
using System.Resources;
using NHibernate.Validator.Engine;

public class CustomMessageInterpolator : IMessageInterpolator
{
    private readonly string ResourceBaseName = "Project.Properties.Validator";

    private readonly ResourceManager resMan;

    public CustomMessageInterpolator()
    {
        this.resMan = new ResourceManager(this.ResourceBaseName, Assembly.GetExecutingAssembly());
    }

    public string Interpolate(string message, IValidator validator, IMessageInterpolator defaultInterpolator)
    {
        var s = GetMessage(message);

        return defaultInterpolator.Interpolate(s, validator, defaultInterpolator);
    }

    private string GetMessage(string message)
    {
        // It's a tempate
        if (!message.StartsWith("{") &amp;&amp; !message.EndsWith("}"))
        {
            return message;
        }

        var resource = message.Substring(1, message.Length - 2);

        var m = this.resMan.GetString(resource, CultureInfo.CurrentCulture);

        if (string.IsNullOrEmpty(m))
        {
            // Returns the original message
            return message;
        }

        return m;
    }
}
{% endhighlight %}

I use the same notation as the original project, putting the resource name between { and }. I try to get the string from Validator.resx in the Properties folder of the project and if not found I return the original message. This way, you can override the default messages with your own ones only if you want.

At the end, all messages are passed to the defaultInterpolator, that manages the rest of the substitutions (for example {Max} and {Min} are replaced by the attribute values).

Finally, you have to configure the interpolator in the .config file (it only worked for me in App.config, not in nhvalidator.cfg.xml):

{% highlight xml %}
<configuration>
  <configSections>
    <section name="nhv-configuration" type="NHibernate.Validator.Cfg.ConfigurationSectionHandler, NHibernate.Validator" />  
  </configSections>
  <nhv-configuration xmlns="urn:nhv-configuration-1.0">
    <property name="message_interpolator_class">Project.Validation.CustomMessageInterpolator, Project.Validation</property>
    <mapping assembly="Project.Domain" />
  </nhv-configuration>
</configuration>
{% endhighlight %}

And that's it. I hope it helps.

