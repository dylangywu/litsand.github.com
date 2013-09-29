---
layout: post
title: "linux timestamp"
description: ""
category: 技术研究
tags: [linux,timestamp]
---
{% include JB/setup %}

linux 文件的访问时间,修改时间,变更时间.

	File: `test.xml'
	Size: 50        	Blocks: 8          IO Block: 4096   regular file
	Device: 801h/2049d	Inode: 1190411     Links: 1
	Access: (0664/-rw-rw-r--)  Uid: ( 1000/ litsand)   Gid: ( 1000/ litsand)
	Access: 2013-09-29 16:09:29.269570245 +0800
	Modify: 2013-09-29 16:06:07.944816506 +0800
	Change: 2013-09-29 16:06:07.972802156 +0800

Access: 通过vi修改文件内容,access time不变.使用cat查看文件内容.access内容改变.

使用vi修改文件内容,access时间不变,modify和change时间发生改变.但是这两个时间不完全一样:

	File: `test.xml'
	Size: 58        	Blocks: 8          IO Block: 4096   regular file
	Device: 801h/2049d	Inode: 1190411     Links: 1
	Access: (0664/-rw-rw-r--)  Uid: ( 1000/ litsand)   Gid: ( 1000/ litsand)
	Access: 2013-09-29 16:09:29.269570245 +0800
	Modify: 2013-09-29 16:11:42.633176800 +0800
	Change: 2013-09-29 16:11:42.661162448 +0800

modify的时间要早于change


使用chmod修改文件权限,change发生改变.:

	➜  /home/l  >chmod 777 tes*xml
	➜  /home/l  >stat test.xml
	File: `test.xml'
	Size: 58        	Blocks: 8          IO Block: 4096   regular file
	Device: 801h/2049d	Inode: 1190411     Links: 1
	Access: (0777/-rwxrwxrwx)  Uid: ( 1000/ litsand)   Gid: ( 1000/ litsand)
	Access: 2013-09-29 16:09:29.269570245 +0800
	Modify: 2013-09-29 16:11:42.633176800 +0800
	Change: 2013-09-29 16:13:35.663211109 +0800


所以修改文件内容,change time也会发生改变.
