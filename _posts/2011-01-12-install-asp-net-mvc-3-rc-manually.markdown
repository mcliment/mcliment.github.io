---
layout: post
status: publish
published: true
comments: true
title: Install ASP.NET MVC 3 Manually
summary: Due to some strange problems in my PC, I had to install this package manually and this is appliable to many other installers
date: 2011-01-12 16:49:08 +0100
categories:
- Programming
tags:
- .NET
- ASP.NET MVC
---
**Update 14-jan-2011:** I found the root solution to all my problems with msiinv, check [my post on the subject](/2011/01/14/how-msiinv-saved-my-day/).

I don't know exactly why but I can't install the MVC 3 package provided by [Web Platform Installer](http://www.microsoft.com/web/downloads/platform.aspx) nor the [standalone one](http://www.microsoft.com/downloads/en/details.aspx?FamilyID=d2928bc1-f48c-4e95-a064-2a455a22c8f6), so I uncompressed it and tried to figure out how to install it manually.

![.NET logo](http://codelog.climens.net/files/2011/01/dotnetlogo.png)

The problem I have is with `vs10-kb2465236-x86.exe` that gives me a `failed with 0x8007066a` error. It seems to be for [enabling Razor syntax](http://support.microsoft.com/kb/2465236/en-us) but the prerequisites are Visual Studio 2010 Ultimate ENU. I have Visual Studio 2010 Professional ESN and that may be the reason or may not.

Anyway, I found the way to install the package manually, although the Razor Intellisense stuff is not available in my setup.

First you have to uncompress the `AspNetMVC3Setup.exe` into a folder and then install the following files (I got the sequence from the `parameterinfo.xml` file, which describes the installation process):

* <del>VS10-KB2465236-x86.exe</del> (it fails in my case, I don't install it)
* AspNetWebPages.msi - ASP.NET Web Pages
* AspNetWebPagesVS2010Tools.msi - ASP.NET Web Pages Visual Studio 2010 Tools
* AspNetMVC3.msi - ASP.NET MVC 3 Runtime
* AspNetMVC3VS2010Tools.msi - ASP.NET MVC 3 Visual Studio 2010 Tools
* NuGet.msi - NuGet

And that's all. In case you have Web Developer Express instead of full Visual Studio, you have ton install the tools specifically for that edition, `AspNetWebPagesVWD2010Tools.msi` instead of `AspNetWebPagesVS2010Tools.msi` and `AspNetMVC3VWD2010Tools.msi` instead of `AspNetMVC3VS2010Tools.msi`.

Good luck!