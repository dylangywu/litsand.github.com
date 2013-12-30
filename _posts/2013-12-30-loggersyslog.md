---
layout: post
title: "logger命令发送文本到syslog"
description: ""
category: 
tags: []
---
{% include JB/setup %}

本来想利用syslog服务把文本文件发送到远程主机。结果发现hp-unix使用的是syslogd，之前测试用rsyslog可以发送文件。而syslogd没找到这个特性。然后搜索资料的时候发现了logger这个命令，可以把文本转换成syslog发送出来。只是hp-unix的logger版本没有直接发送到远程主机的选项。而且logger不能实时的监控文件，所以采用一个曲折的办法，没发送一次清空文件内容。

大致的命令如下：

	cat /var/weblogic/access.log > /var/tosyslog.txt && echo "" > /var/weblogic/access.log && logger -p user.emerge -f /var/tosyslog.txt

user.emerge 是syslog协议规定的事件来源是等级，可以修改。

