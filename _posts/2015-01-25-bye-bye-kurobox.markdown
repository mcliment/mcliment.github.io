---
layout: post
status: publish
published: true
comments: true
title: Bye bye Kurobox
summary: A deserved farewell to a device that has worked flawlessly for so many years
date: 2015-01-25 00:15:00 +0100
categories:
- projects
tags:
- nas
- kurobox
- hardware
---

    Broadcast message from root@kurobox (pts/0) (Sat Jan 24 23:55:26 2015):
    The system is going down for system halt NOW!

With this message my [Kurobox](http://buffalo.nas-central.org/wiki/Category:Kurobox) turned off -actually it didn't because something is broken in the ACPI kernel stuff- for the last time.

I've just replaced it by a brand new [Synology DS215j](https://www.synology.com/products/DS215j). 

## Some history

I acquired this machine from an online store called Revogear.com -now extinct- in November 2006, more than eight years ago. I can't think how many things happened since then, in my professional and personal life and obviously in this unstoppable tech world.

But there it stood, running _almost_ 24/7 since I installed it at my parents' home where I lived then. It was very appealing: a small sized low power Linux server with 128Mb of RAM, a [MPC8241 RISC processor](http://www.freescale.com/webapp/sps/site/prod_summary.jsp?code=MPC8241) @266MHz, a 3.5" HD IDE bay, Gigabit Ethernet and a single USB 2.0 port.

It really was a rebranded [Buffalo Linkstation HG](http://buffalo.nas-central.org/wiki/Category:HG) with some modifications to make it more customizable and a nice Kanji logo on the side. More customizable didn't mean _easily_ customizable, because it was pretty low level stuff using `netcat` and sending some esoteric commands over the wire. But that was pretty fun, at least for me 8 years ago.

<a href="http://commons.wikimedia.org/wiki/File:Buffalo_kuro-box.jpg#mediaviewer/File:Buffalo_kuro-box.jpg" title="'Buffalo kuro-box' by Saoyagi2. Licensed under CC0"><img src="http://upload.wikimedia.org/wikipedia/commons/thumb/d/d7/Buffalo_kuro-box.jpg/640px-Buffalo_kuro-box.jpg" alt="Buffalo kuro-box" /></a>

After the Kurobox HG, Buffalo released the PRO, which changed the processor architecture from PowerPC to the now popular ARM and over the years the project was discontinued and forgotten. At the beginning it looked promising and there was a vibrant community and many pages and forums to find and ask stuff but soon started to languish with the poor effort from Buffalo to continue with this series. 

As of today, many Linux distros still provide PowerPC packages but the main support is for Intel x86 platform. On the other hand, [ARM is gaining traction](http://electronics.stackexchange.com/a/42077), specially in the NAS field, as Synology, QNAP and others are using architecture for their low-end products. The [Debian Popularity Contest](http://popcon.debian.org/) has some figures about this fact, although it still offers good support for PowerPC. Fedora Project [dropped 32bit PowerPC support](http://fedoraproject.org/wiki/Architectures/PowerPC#Supported_Architectures) four releases ago.

## Obsolescence

There are several problems that have arisen with my Kurobox during its lifetime. I don't know which one of them is the most important but the sum of them made me replace it.

Having just an IDE bay does not help. It's not difficult at all to find IDE disks -and they are cheap- but they are not larger than 160-250Gb. Mine is 250Gb and it's always full and slow. Even if I don't put all my backups there -mainly photos- there's not enough room. I know I can add an external USB drive but that idea is not a good idea because it won't perform well given the limited hardware resources.

The processor was modest at the time of purchase but now is completely outdated. This is a bottleneck for file transfers, no matter if I use SCP, SMB or NFS, the transfer rate is never better than 1MB/s and generally is about 600-700KB/s. Compiling a kernel took about 20-24h...

As it is the HG version it came with 128Mb RAM soldered on the motherboard and it's a tight number to run a lot of processes. I have used it as a test web server with a LAMP stack -it was [lighttpd](http://www.lighttpd.net/) instead of Apache-, SSH server, mail server with [Courier MTA](http://www.courier-mta.org/), DNS server with [djbdns](http://cr.yp.to/djbdns.html), [MLdonkey](http://mldonkey.sourceforge.net/Main_Page) server and [rtorrent](http://rakshasa.github.io/rtorrent/)/[Transmission](https://www.transmissionbt.com/) headless server. This was really too much: in the last years I have only used it as a NAS with a Transmission server. It worked, just required some patience.

Finally, upgrading the kernel or installing a new Linux distro are difficult tasks. I wanted to update the original kernel a couple of years ago because it was too outdated and the thing is facing Internet so, from a security standpoint was a good idea. The documentation was too hard to find -mostly at [Buffalo@NAS-Central](http://buffalo.nas-central.org/wiki/Main_Page)-, many 404, many pages in Japanese and too many visits to (https://archive.org/) to find just some instructions on how to do it. I succeeded but was not _that_ fun and some stuff is broken, specially the ACPI part as I can't reboot and shutdown anymore -unless I unplug the cord-.

## The newcomer

Now I'm pretty happy :happy: with the Synology ds215j. I don't like that is not as open as the Kurobox but the default software is really cool and works out of the box. Nowadays I value more my free time and I don't enjoy that much hacking with old rare hardware.

So, farewell ol' good Kurobox!