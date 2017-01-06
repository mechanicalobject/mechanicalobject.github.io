---
layout: post
author : Mechanical Object
category: Web
title: Migration from Tfs 2013 to Tfs 2015
tagline: 
tags: [tfs2013, tfs2015, tfs on premises, migration]
published : true
---

Here is the protocol that I applied to migrate our tfs 2013 to tfs 2015.

<!--more-->

## Important 

1. The protocol is only tested to migrate 
* tfs 2013 to tfs 2015 
* tfs 2015 to tfs 2017
2. The protocol is only valid for TFS on premises 

## Protocol 

### Preparation 

0. Define a deadline for code freeze
1. Checkin / shelve all ongoing developments before the deadline
2. Finalize forward/reverse integration in case you have multiple branches
3. Backup project collection database, suggested format : ```Tfs_CollectionName_YYYYMMDD```

The following sql script is quite handy. Don't forget to replace CollectionName and the date in YYYYMMDD format. Please note 
that ```\\server\backup\...``` is a shared network path acessible by the machine that hosts target tfs server.

{% highlight sql %}
BACKUP DATABASE Tfs_CollectionName TO  DISK = N'\\server\Backup\Tfs_CollectionName_YYYYMMDD.bak' 
WITH NOFORMAT, NOINIT,  NAME = N'Tfs_CollectionName-Full Database Backup', SKIP, NOREWIND, NOUNLOAD,  STATS = 10, COPY_ONLY 
GO
{% endhighlight %}

### Migration 

4. Stop the project collection
5. Detach the project collection 
6. Backup **again** project collection database, suggested format : ```Tfs_CollectionName_YYYYMMDD_Detached```
7. Restore the backups on target tfs database 
8. Using target tfs admin console, attach restored database 

### Verification 
9. Check changesets history (right click on a branch => view history)
10. Check workitems (epics, features etc.)
11. Check users and their permissions 
12. Check build definitions if you have any
13. Check distributed build agents, if you have any

### Bonus 
* If you have developed custom plugins, mvcsmon can be of great help to debug these remotely
