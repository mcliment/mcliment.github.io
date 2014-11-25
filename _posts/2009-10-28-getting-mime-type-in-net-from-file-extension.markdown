---
layout: post
status: publish
published: true
comments: true
title: Getting MIME type in .NET from file extension
summary: Discuss some strategies on how to get MIME types and a proposes a solution based on a dictionary of known extensions
date: 2009-10-28 15:00:49 +0100
categories:
- Programming
tags: []
---
After some research, I [posted a question](http://stackoverflow.com/questions/1612767/file-extensions-and-mime-types-in-net) at [stackoverflow.com](http://stackoverflow.com/) without much success, but the problem was that the answer was already there.

I you are working on any application that needs to serve files on it's own, for example for encrypted storage or a rich ACL that needs to be enforced, you will need to provide _the most correct Content-Type possible or MIME type from a given file extension_ to the reponse in order to achieve the best user experience.

For example, if you serve a PDF and use a generic MIME like application/octet-stream, the user may not se a PDF reader to open it, depending on the browser and platform.

Then, in .NET there are 4 alternatives to do this, with their pros and their cons:

* **Use [the Windows registry](http://stackoverflow.com/questions/1029740/get-a-mime-from-an-extention/1029796#1029796)** - This may be a good solution for desktop applications but depends on the software installed on the machine and typically web servers don't have Acrobat or Office.
* **Use [urlmon.dll's FindMimeFromData](http://stackoverflow.com/questions/58510/using-net-how-can-you-find-the-mime-type-of-a-file-based-on-the-file-signature/58570#58570)** - I don't know exactly the efectiveness of this method, but it's used internally by Windows. It should give good and reliable information but you have to read the file and give the method up to 256 bytes of the file header.
* **Use [IIS information](http://stackoverflow.com/questions/174888/asp-net-iis6-how-to-search-the-servers-mime-map/174988#174988)** - Maybe the most obscure mechanism, based on Directory Services and COM stuff. It's based on the same mechanism IIS uses, so it should be quite reliable, but complex and may not be the fastest.
* **Use your own dictionary** - Maybe not the most elegant but even .NET uses this internally (inspect `System.Web.MimeMapping` with [ILSpy](http://ilspy.net/) if you are curious). You get a bunch of types [from somewhere](http://svn.apache.org/repos/asf/httpd/httpd/trunk/docs/conf/mime.types) and add them to a dictionary with the associated extension. Easy.

So, the options are clear. Finally in my case, I used a simple dictionary to ensure compatibility between platforms and homogeneity. Using other mechanisms could lead to strange errors on some platforms that I wanted to avoid over other benefits. But there are [much more elaborate mechanisms](http://stackoverflow.com/questions/1612767/file-extensions-and-mime-types-in-net/1623612#1623612) that can be done.