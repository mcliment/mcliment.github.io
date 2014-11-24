---
layout: post
status: publish
published: true
title: Deployment web site Zip with MsBuild and TeamCity
summary: I needed to make a Zip file of a web site and automate it with TeamCity. I used MsBuild and some obscure tricks
date: 2010-11-30 12:09:28 +0100
categories:
- Programming
tags:
- subversion
- continuous integration
- teamcity
- msbuild
---
One of the most useful practices that I've been using during the last years is continuous integration and for that purpose, [TeamCity](http://www.jetbrains.com/teamcity/) is the tool of choice, which offers a free professional version with up to 20 users and 20 build configurations, enough for a small team.

![TeamCity Logo](/images/teamcity-logo.png)

Recently I tried (again, first time I found some trouble) to create an [MsBuild](http://msdn.microsoft.com/en-us/library/0k6kkbsd.aspx) script to make a web publication to a folder an then zip that folder in order to make a simple distributable with all the things needed to set up our web project that anyone in the organization could download and install, instead of having to create that zip myself or publishing through Visual Studio.

Finally, I got the script and seems to work using [MsBuildCommunityTasks](https://github.com/loresoft/msbuildtasks) and some community knowledge.

The first part deals with publishing the site:

{% highlight xml %}
  <PropertyGroup>
    <OutputFolder>$(MSBuildProjectDirectory)\publish</OutputFolder>
    <TempFolder>$(MSBuildProjectDirectory)\temp\</TempFolder>
  </PropertyGroup>
  <Target Name="PublishSite">
    <Message Text="Publishing in folder: $(OutputFolder)" />
    <RemoveDir Directories="$(OutputFolder);$(TempFolder)" ContinueOnError="true" />
    <MSBuild
      Projects="src\Adapting.Web\Adapting.Web.csproj"
      Targets="ResolveReferences;_WPPCopyWebApplication"
      Properties="Configuration=Release;WebProjectOutputDir=$(OutputFolder);OutDir=$(TempFolder)"
      StopOnFirstFailure="true" />
    <RemoveDir Directories="$(TempFolder)" ContinueOnError="true" />
  </Target>
{% endhighlight %}

I define some variables and then I call the `_WPPCopyWebApplication` which is the important target (in .NET 2.0 was `_CopyWebApplication`). This should be the same as publishing a web site to a folder using the wizard.

Then, I get the files in the `OutputFolder` and make a zip with them:

{% highlight xml %}
  <Target Name="PackageSite" DependsOnTargets="PublishSite">
    <ItemGroup>
      <PublishFiles Include="$(OutputFolder)\**" />
    </ItemGroup>
    <Message Text="Packaging Site" />
    <Zip
      Files="@(PublishFiles)"
      ZipFileName="release-web.zip"
      WorkingDirectory="$(OutputFolder)"
      />
  </Target>
{% endhighlight %}

I simplified this a little bit because I have as well a target that generates the build number from SVN and changes the `AssemblyInfo.cs` and uses this numbers to name the zip.

In TeamCity, you can create a new configuration with `*.zip` as artifacts, that points to your SCM (Subversion in my case) and the most important thing is that uses the MsBuild runner targeting `PackageSite`.

The only thing that was that if I put the `<ItemGroup>` section out of the `<Target>`, works on the command line but not inside TeamCity.

That's all, I hope this helps.

Sources:

* [How to publish a web site with MsBuild](http://codingcockerel.co.uk/2008/05/18/how-to-publish-a-web-site-with-msbuild/)
* [Using MsBuild to create a deployment zip](http://blog.benhall.me.uk/2008/09/using-msbuild-to-create-a-deployment-zip/)
