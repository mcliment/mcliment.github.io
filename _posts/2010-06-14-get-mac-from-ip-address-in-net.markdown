---
layout: post
status: publish
published: true
title: Get MAC from IP address in .NET
summary: There's not any method in the .NET standard classes to get the MAC from the IP, so I explain how to get it using Windows native dlls
date: 2010-06-14 09:38:36 +0200
categories:
- Programming
tags:
- .NET
- networking
---
After dealing with several options, most of them involving a call to a command line tool and parsing the output, which is not really elegant. Other solutions were based on obsolete VBScript client code and ActiveX, even worse.

![Pinvoke.net](/images/pinvokelogo.png)

Finally I found in the excellent [pinvoke.net](http://www.pinvoke.net/) site [a very nice and simple example](http://www.pinvoke.net/default.aspx/iphlpapi.SendARP) using a simple call to `iphlpapi.dll`'s SendARP method. The trick was to use [ARP](http://en.wikipedia.org/wiki/Address_Resolution_Protocol) to get the MAC from the IP.

Note that this has it's limitations, as I will only work if the remote machine is in the same network and the packet does not need to go through a router. I was enough for our needs.

Here's the code I adapted from the example:

{% highlight csharp %}
public static string GetMAC(IPAddress ipAddr)
{
    var mac = new byte[6];
    var len = (uint)mac.Length;

    var result = SendARP((int)ipAddr.Address, 0, mac, ref len);

    if (result != 0)
    {
        return "MAC not found!";
    }

    var str = new string[(int)len];

    for (var i = 0; i &lt; len; i++)
    {
        str[i] = mac[i].ToString("x2");
    }

    return string.Join(":", str);
}

[DllImport("iphlpapi.dll", ExactSpelling = true)]
private static extern int SendARP(int destIP, int srcIP, byte[] pMacAddr, ref uint phyAddrLen);
{% endhighlight %}

Enjoy!