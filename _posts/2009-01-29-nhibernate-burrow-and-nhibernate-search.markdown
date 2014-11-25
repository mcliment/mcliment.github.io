---
layout: post
status: publish
published: true
comments: true
title: NHibernate Burrow and NHibernate Search
summary: NHibernate.Search and NHibernate.Burrow can play nice together using NHibernate events instead of interceptors.
date: 2009-01-29 11:50:38 +0100
categories:
- Programming
tags:
- .NET
- NHibernate
- NHibernate.Burrow
- NHibernate.Search
---
[Spanish version](http://blog.climens.net/2009/01/27/nhibernate-burrow-y-nhibernate-search/)

In this post I'm going to explain how I managed to make these projects to work together using NHibernate events instead of the classic interceptors. Through these events, we tell NHibernate.Search to index the elements marked for that purpose without touching the session. What we need to perform a search in the index is the id and we get it from [NHibernate.Burrow](http://nhforge.org/wikis/burrow/home.aspx).

The first thing we need is to configure the application properly (`App.config`):

{% highlight xml %}
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <configSections>
    <section name="NHibernate.Burrow" type="NHibernate.Burrow.Configuration.NHibernateBurrowCfgSection, NHibernate.Burrow" />
    <section name="nhs-configuration" type="NHibernate.Search.Cfg.ConfigurationSectionHandler, NHibernate.Search" />
  </configSections>
  <NHibernate.Burrow>
    <persistenceUnits>
      <add name="PersistenceUnit1"
           nh-config-file="hibernate.cfg.xml" />
    </persistenceUnits>
  </NHibernate.Burrow>
  <nhs-configuration xmlns='urn:nhs-configuration-1.0'>
    <search-factory>
      <property name='hibernate.search.default.directory_provider'>NHibernate.Search.Store.RAMDirectoryProvider, NHibernate.Search</property>
      <property name='hibernate.search.default.indexBase'>~/Index</property>
    </search-factory>
  </nhs-configuration>
</configuration>
{% endhighlight %}

NHibernate.Search does not manage the configuration section properly, so it looks for a section called `nhs-configuration`, so don't change the name. On the other side, we should provide a valid `hibernate.cfg.xml` with NHibernate configuration. For example, for testing you can use an in-memory SQLite database:

{% highlight xml %}
<?xml version="1.0" encoding="utf-8" ?>
<hibernate-configuration xmlns="urn:nhibernate-configuration-2.2">
  <session-factory>
    <property name="connection.provider">NHibernate.Connection.DriverConnectionProvider</property>
    <property name="dialect">NHibernate.Dialect.SQLiteDialect</property>
    <property name="connection.driver_class">NHibernate.Driver.SQLite20Driver</property>
    <property name="connection.connection_string">Data Source=:memory:;Version=3;New=True;</property>
    <property name="connection.release_mode">on_close</property>
    <mapping assembly="Mapped.Assembly" />
    <mapping assembly="Mapped.Assembly2" />
  </session-factory>
</hibernate-configuration>
{% endhighlight %}

Don't forget to add all the assemblies in the `mapping-assembly` sections.

After that, the most interesting part is Burrow session initialization in order to add reference to the events. For example, this is what I have at the beginning of each test case that hits the database:

{% highlight csharp %}
var framework = new BurrowFramework();

Configuration config = framework.BurrowEnvironment.GetNHConfig("PersistenceUnit1");

// Poner los EventListeners
config.SetListener(ListenerType.PostDelete, new FullTextIndexEventListener());
config.SetListener(ListenerType.PostInsert, new FullTextIndexEventListener());
config.SetListener(ListenerType.PostUpdate, new FullTextIndexEventListener());

framework.BurrowEnvironment.RebuildSessionFactories();
{% endhighlight %}

The last line is important because it updates the session with the changes made. Ok, this may not be the best approach and adding the events in the nhibernate.cfg.xml would be better. It's up to you. It should be something like this (the code goes into the _session-factory_ section):

{% highlight xml %}
<listener class="NHibernate.Search.Event.FullTextIndexEventListener, NHibernate.Search" type="post-update" />
<listener class="NHibernate.Search.Event.FullTextIndexEventListener, NHibernate.Search" type="post-insert" />
<listener class="NHibernate.Search.Event.FullTextIndexEventListener, NHibernate.Search" type="post-delete" />
{% endhighlight %}

With this code you don't need to rebuild the session factory.

With this configuration, the indexable entities -see Dario Quintana's [post on NHibernate.Search](http://darioquintana.com.ar/blogging/?p=21)- are managed automatically by the library without doing anything manually and in a transparent manner.

Finally, you only need to test the search over the indexes. In the simple test below, the code creates a NHibernate.Search session and makes a search over an indexed entity, following Dario's example:

{% highlight csharp %}
[Test]
public void SearchIndexedUser()
{
    var session = Search.CreateFullTextSession(new BurrowFramework().GetSession());

    var qp = new QueryParser("UserName", new StopAnalyzer());
    var nhQuery = session.CreateFullTextQuery(qp.Parse("test"), typeof(User));

    var results = nhQuery.List();

    Assert.AreEqual(1, results.Count);
}
{% endhighlight %}

You must take in account on important thing: NHibernate.Search uses the current transaction if there's one and NHibernate.Burrow makes use of them, so if you want to persist the indexes before making the search, it's not enough evicting the entity. You should do the following:

{% highlight csharp %}
new BurrowFramework().CloseWorkSpace();
new BurrowFramework().InitWorkSpace();
{% endhighlight %}

With this piece of code we finish Burrow's pending transaction and create a new one in order to persist all the index changes.

And this is all. With this samples you can integrate in a simple manner two interesting projects on top of NHibernate, that can be a bit tricky if you need to deal directly with the session and are using Burrow. As well, using the new event system introduced in the 2.0 branch of NHibernate avoids the use of interceptors that in many cases was painful.

Note that both projects were compiled from [the trunk of nhcontrib](https://svn.code.sf.net/p/nhcontrib/code/trunk/) against [NHibernate 2.1.0GA](https://github.com/nhibernate/nhibernate-core/releases/tag/2.1.0GA).

I'll be waiting for your comments.