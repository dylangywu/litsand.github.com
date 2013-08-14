---
layout: post
title: "smb null session authentication"
description: "技术研究"
category: 
tags: [windows,smb]
---
{% include JB/setup %}

还是在纠结windows产生的日志的问题,在逐条排除,看看是哪个服务产生的日志.

![nessus报告]({{site.img_url}}smbnull.png)

根据报告中提到的方法操作,就可以关闭smb null session authentication.

	Set :  - HKLM\SYSTEM\CurrentControlSet\Control\LSA\RestrictAnonymous=1
	- HKLM\SYSTEM\CurrentControlSet\Services\lanmanserver\parameters\restrictnullsessaccess=1
	Remove BROWSER from :
	- HKLM\SYSTEM\CurrentControlSet\Services\lanmanserver\parameters\NullSessionPipes

Reboot once the registry changes are complete.

设置和删除两个操作.
