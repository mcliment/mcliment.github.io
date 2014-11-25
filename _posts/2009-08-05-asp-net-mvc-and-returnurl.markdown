---
layout: post
status: publish
published: true
comments: true
title: ASP.NET MVC and ReturnUrl
summary: Some best practices to use the ReturnUrl parameter correctly in ASP.NET MVC
author:
  display_name: Marc Climent
  login: climens
  email: climens@climens.net
  url: ''
author_login: climens
author_email: climens@climens.net
wordpress_id: 227
wordpress_url: http://codelog.climens.net/?p=227
date: !binary |-
  MjAwOS0wOC0wNSAwOToxMToyNyArMDIwMA==
date_gmt: !binary |-
  MjAwOS0wOC0wNSAwNzoxMToyNyArMDIwMA==
categories:
- Programming
tags:
- .NET
- ASP.NET MVC
---
I've seen many ASP.NET MVC samples and some of them do not honor de `ReturnUrl` parameter, rendering this unusable.

Classic ASP.NET has the login form component that puts the same exact URL used to access the login page in the form action URL, preserving the `ReturnUrl` parameter and then, `FormsAuthentication.GetReturnUrl()` gets it from the `Request.QueryString` collection.

Fortunately, the `GetReturnUrl()` method looks as well in `Request.Form` in case there's nothing in the query, this way, we can use a hidden field as well to store the `ReturnUrl` in the View and pass it through POST when the login form is submitted.

So, in ASP.NET MVC we have two options:

* Use simply `Html.BeginForm()` without parameters, which will put a simple form tag, with the same URL as the current one and use POST by default
* If we want to have more control on the `Html.BeginForm()`, put a _hidden field_ with the value of the `ReturnUrl`, **only if the ReturnUrl is not empty**, or it will fail. This can be obtained from the `Request.QueryString` collection, the ViewData if we put it previously on the Controller action or even better store it in the ViewModel and have a strong typed reference.

And that's it. Then, `FormsAuthentication.GetReturnUrl()` will get the proper URL and other methods like `FormsAuthentication.RedirectFromLoginPage()` will also work seamlessly.