---
layout: post
status: publish
published: true
comments: true
title: Troubleshooting MSDTC, RPC and NServiceBus issues
summary: MSDTC is rare and can cause many headaches. This is the process I followed to track down the problems I was having
date: 2011-06-06 18:19:47 +0200
categories:
- Programming
tags:
- nservicebus
- msdtc
- rpc
- troubleshooting
---
At first it seemed easy, but it's been a long distance hurdle race. At the end, I have solved the problems I've found and my painful experience can be useful for other people, so this is it.

![NServiceBus old logo](/images/nservicebus.png)

First of all, my topology is the following: a client, in this case for testing purposes, my machine, which is running an ASP.NET web application and a NServiceBus host that processes all the messages. This has been configured following [the official documentation](http://docs.particular.net/) and [looking at the samples](https://github.com/Particular/NServiceBus.Msmq.Samples) (check out the full [NServiceBus source code](https://github.com/Particular/NServiceBus), you'll need it). It's a bit messy but with some trial and error you can make sense of it. There are some obscure properties that have to be googled to know what they mean.

Processing the messages is not trivial for the bus host. Some of them attack the database and need to use MSDTC to manage the transactions. This means that MSDTC must be up and running without problems.

One trick I learned after a lot of researching is [the `Runner.exe` tool from NServiceBus](https://web.archive.org/web/20120331064930/http://blog.zoolutions.se/post/2010/04/01/Conquering-NServiceBus-part-5-e28093-Troubleshooting-DTC.aspx). This comes with NServiceBus source code (and I think that also with paid version) and ends up in `build\tools\MsmqUtils` if you build it with `build.bat` or `build-net4.bat`. Just open a command line as administrator and run:

`Runner.exe /i`

This will install MSDTC and configure it properly. I don't think you'll need anything else but check the MSDTC configuration in _Component Services -> Computers -> My Computer -> Distributed Transaction Coordinator -> Local DTC_. Right click to go to properties and ensure everything is configured like in this [Troubleshooting DTC](https://web.archive.org/web/20120331064930/http://blog.zoolutions.se/post/2010/04/01/Conquering-NServiceBus-part-5-e28093-Troubleshooting-DTC.aspx) post.

At this point, I recommend you to disable the Windows Firewall on both machines, at least for domain network, just to be sure it's not interfering and breaking the tests, we will solve this later.

Then, [DTCPing](http://www.microsoft.com/downloads/details.aspx?displaylang=en&FamilyID=5e325025-4dcd-4658-a549-1d549ac17644) is your friend. This small tool will check the DTC connectivity between two machines. It's a bit awkward. You have to start the tool in both machines, then put the name of the other and click ping on each one. This will make a test and if everything goes well, you'll see a detailed log without errors:

~~~~~~~~
++++++++++++Validating Remote Computer Name++++++++++++
Please refer to following log file for details:
	C:\tools\DTCPing\LOCALMACHINE2928.log
Invoking RPC method on REMOTEMACHINE
RPC test is successful
++++++++++++RPC test completed+++++++++++++++
++++++++++++Start DTC Binding Test +++++++++++++
Trying Bind to REMOTEMACHINE
Received reverse bind call from REMOTEMACHINE
Binding success: LOCALMACHINE-->REMOTEMACHINE
++++++++++++DTC Binding Test END+++++++++++++
Please send following LOG to Microsoft for analysis:
	Partner LOG: REMOTEMACHINE5928.log
	My LOG: LOCALMACHINE2928.log
++++++++++++Start Reverse Bind Test+++++++++++++
Received Bind call from REMOTEMACHINE
Trying Reverse Bind to REMOTEMACHINE
Reverse Binding success: LOCALMACHINE-->REMOTEMACHINE
++++++++++++Reverse Bind Test ENDED++++++++++
~~~~~~~~

Here started my nightmare, I got the damned: `RPC error 5 (Access denied)`. After hours googling for the problem I found [this post about MSDTC problems](http://blogs.msdn.com/b/distributedservices/archive/2008/11/12/troubleshooting-msdtc-issues-with-the-dtcping-tool.aspx) (I was looking for RPC problems, which actually was the root cause). The cause is that remote RPC is disabled in Windows workstations by default.

I opened for the N-th time the registry and added the RPC key to `HKLM\Software\Policies\Microsoft\Windows NT` and the `RestrictRemoteClients` DWORD value to 0. Rebooted without much hope. Magically, DTCPing worked and life seemed better and brighter.

Then, the Windows Firewall must be configured. In Windows Server 2008 (or Windows 7, it's the same interface), go to _Control Panel -> System and Security -> Windows Firewall_ (or just execute `firewall.cpl` at the command prompt). Go to _Advanced Configuration_ to open a new window. Go to inbound rules and look for the Distributed Transaction Coordinator. If they are disabled, enable them. On the workstation the firewall can be enabled. Beware that DTCPing may not work, because the Firewall is only allowing connections to/from `svchost.exe` and `msdtc.exe` (if you want it to work, add a rule for that process).

*[MSDTC]: Microsoft Distributed Transaction Coordinator