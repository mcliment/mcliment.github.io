---
layout: post
status: publish
published: true
comments: true
title: Lessons learned optimizing MySQL
summary: A summary of some things I discovered while trying to optimize the performance of a production MySQL server.
date: 2011-08-05 13:41:18 +0200
categories:
- Programming
tags:
- MySQL
- performance
- tips
- optimization
---
This week I've been quite entertained at work optimizing a big [MySQL database](http://dev.mysql.com/), 18M rows and about 10Gb of table data for a high traffic web site (11M unique views per month). Not optimizing for performance is overkill in this case, so I have been learning and enjoying a lot during these days.

The first thing to note is that **database servers are complex beasts** (don't underestimate them) and each and every database provider has its own strengths but also its own caveats.

![MySQL logo](/images/logo-mysql.png)

Sooner or later you'll face a behavior that may seem counter-intuitive and there's when you must **read a lot of documentation**. In this case, [MySQL Performance Blog](http://www.mysqlperformanceblog.com/) and [MySQL documentation](http://dev.mysql.com/doc/refman/5.5/en/) have been of great help, as well as the now unmantained-but-still-useful [Hack MySQL](http://hackmysql.com/) site, which provides great tips very well explained.

Second. As always, **doing an 80% optimization consumes 20% of the time** and most of it is quite straightforward. `EXPLAIN`, review results, create index, `EXPLAIN` again. This way you can do a good job just knowing some indexing basics. Your indexes may be a bit fat, but will do the job.

Then you'll need to **be patient**, creating indexes is not always fast, in fact the number, size and heterogeneity of the columns involved will increase the time it takes to create them. While you wait, the best is to read good books about MySQL or relational databases like: [Relational Database Index Design and the Optimizers](http://www.amazon.com/gp/product/0471719994/ref=as_li_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=0471719994&linkCode=as2&tag=climecodel-20&linkId=ZJBKQJURN7Z5WTGN).

You'll need to **be organized**. Creating indexes randomly just looking a couple of slow `SELECT`s is something, but you must look at the big picture: make an inventory of all the application queries, no matter how unimportant look like because if they attack a big table with a `WHERE` or an `ORDER BY` they will perform bad. Fact. This list will help you organize the indexes and see which ones are used and which not and where.

Learn as well **what the configuration parameters mean**, MySQL has a bunch of them. Just increasing numbers will not always solve your bottlenecks. If you see something strange, there might be a parameter misconfigured and causing bad results.

Sometimes you'll **find yourself frustrated** by something that you can't understand. I found that almost anything in this field has an explanation, even a reasonable one.

Finally, don't overlook other thinks. **Database performance is important** but there may be a lot of room to improve in your code, don't blame MySQL as the root of all problems!