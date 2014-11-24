---
layout: post
status: publish
published: true
title: How msiinv saved my day
summary: mssinv is a tool to manage MSI installed packages and I used it to track a problem with a package partially installed, that had to be removed with this tool
date: 2011-01-14 12:22:31 +0100
categories:
- Programming
tags:
- ASP.NET MVC
- MSI
---
In my [previous post](/2011/01/12/install-asp-net-mvc-3-rc-manually/) I reported my problems installing MVC3 in my machine. That led me to further investigation as that worked perfectly in my co-workers' machines.

I tried to repair Visual Studio 2010 and told me that it had troubles with `vs_setup.msi`... strange. Looking around I found a post that led me to [an explanation of the msiinv.exe tool](http://blogs.msdn.com/b/astebner/archive/2005/07/01/using-msiinv-to-gather-information-about-what-is-installed-on-a-computer.aspx).

It took me a while to get the tool as the author's site is broken, but Stackoverflow [came on rescue](http://stackoverflow.com/questions/239264/anyone-got-a-copy-of-msiinv-exe).

Then, I ran the tool:

    msiinv -p &gt; msiinv.txt

Then opened the file and... surprise!

~~~~~~~~
Microsoft Visual Studio 2010 Professional - ESN
    Product code:    {725041D1-9F45-30D3-A78E-DF08C4E1A297}
    Product state:    (1) The product is advertised, but not installed.
    Package code:    {9E58363D-E4A9-49D3-82B7-42584AFCA9E7}
    AssignmentType:    1
    Language:    3082
        Package:    vs_setup.msi
    0 patch packages.
~~~~~~~~

Strange. I tried to fix it with MSI:

    msiexec /f {725041D1-9F45-30D3-A78E-DF08C4E1A297}

It asked me for the `vs_setup.msi`, it's in the root of the Visual Studio 2010 DVD in case it's not found automatically. Magically it worked and now it's installed:

~~~~~~~~
Microsoft Visual Studio 2010 Professional - ESN
    Product code:    {725041D1-9F45-30D3-A78E-DF08C4E1A297}
    Product state:    (5) Installed.
    Package code:    {9E58363D-E4A9-49D3-82B7-42584AFCA9E7}
    Version:    10.0.30319
    AssignmentType:    1
    Publisher:    Microsoft Corporation
    Language:    3082
       Installed from: D:\VS2010\
    Package:    vs_setup.msi
    Help link:    http://go.microsoft.com/fwlink/?LinkId=133405
    Local package:    C:\Windows\Installer\3f3bc84.msi
    Install date:    2011\01\14
    0 patch packages.
~~~~~~~~

Great! Now the ASP.NET MVC3 RTM setup package worked like a charm.