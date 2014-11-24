---
layout: post
status: publish
published: true
title: Using Dreamhost backup account with rsync
summary: Now that Dreamhost gives for free 50GB of backup space for thrier customers, get the most of it by using rsync and not FTP
date: 2008-12-03 13:06:09 +0100
categories:
- Linux
tags:
- backup
- dreamhost
- rsync
---

![Dreamhost Logo](/images/blog-dh-logo-final.jpg)

This summer Dreamhost launched their **50Gb backup** account which was great news but unfortunately they only offered FTP access. In the October newsletter they announced rsync and SCP support for the backup user using [RSSH](http://www.pizzashack.org/rssh/index.shtml) and I will now show you how to set up an automatic script to back your valuable data up. That is quite cool, so don't hesitate to [sign-up with them](http://www.dreamhost.com) and try out this feature.

The first thing is configuring the account for SCP/SFTP/rsync access. Go to the [Backups User section](https://panel.dreamhost.com/index.cgi?tree=users.backup) of the Users menu in the Panel and create a user. They'll give you a name and a password and send you an email with all the information. That's the only thing you need.

Then, you must create the folder structure to hold your backups in case you don't want to have everything on the root folder. The easiest thing is to get a SFTP client. In my case I used [WinSCP](http://winscp.net/), but you can use whatever you want. I created a folder called photos to hold my digital collection.

Now, the trickiest part. If you have a modern Linux distribution or any up to date _rsync_ compilation it's very possible that you have version 3.0 or later that implements protocol version 30. To know the version of _rsync_, just write:

    rsync --version

In my case, I have `rsync  version 3.0.3  protocol version 30`. In that case, if you use _rsync_ as usual, you'll get a nice error saying that:

    insecure -e option not allowed.
    This account is restricted by rssh.
    Allowed commands: scp sftp rsync

After googling a little, I discovered that protocol 30 sends implicitly an -e command and the installed version in Dreamhost does not like that, because it uses protocol 29. The solution is adding `--protocol 29` to the rsync options.

Then, to make the whole process automatic, you need to avoid _rsync_ asking for a password. That can be easily done following this instructions on [passwordless ssh](http://web.archive.org/web/20050908010949/http://blogs.translucentcode.org/mick/archives/000230.html). Note that you can't ssh to the remote machine but if you create a new file called `authorized_keys` locally with the contents of `id_dsa.pub` and then using the SFTP client upload it to a new folder -if not exists- called `.ssh` (don't forget the dot!). It will work like a charm.

Then, create a script that does the _rsync_ thing. In my case, I have this single instruction, but you can sync as many folders as you want.

    rsync -aP --delete --protocol=29 /mnt/photos/* bXXXXXXX@backup.dreamhost.com:photos

With this, I tell _rsync_ to use archive mode (`-a`), which is quite interesting as it preserves timestamps and is recursive, and to store partial information (`-P`) in case I break the connection. Then I tell _rsync_ to delete the destination files that are not in the source (which can be dangerous if you delete something locally and want to recover it later, so I leave this option up to you). Then to use protocol version 29 (`--protocol 29`) as I discussed earlier. Finally, I tell _rsync _which is the source folder and the destination one, indicating the username and the host.

And that's it. If you store this command in a `.sh` file and put it in the crontab (with `crontab -e`), you can automatically back up your valuable data to the Dreamhost backup account. My crontab settings are like this:

    # m h  dom mon dow   command
    0 2 * * * ~/backup.sh > ~/backup.log

This will execute the backup script every day at 2 AM, and will store a `.log`, in case I want to check the results. Note that the first time that you execute the script it may take some time (4 days for me, 18Gb in total) depending on the amount of data to back up, so I recommend you to execute it manually before creating an automatic task.

Finally, I want to remind you that the data stored in that account is not guaranteed by any means by Dreamhost, so don't make it your only full trusted source for backup data.

Note: This is my first post in English, so please forgive my errors, I tried my best. :-)

