---
layout: post
title: "kernel source mismatch"
description: ""
category: 
tags: [kernel,centos]
---
{% include JB/setup %}

uname -r 显示内核是2.6.18-164.el5

	yum install kernel-devel
	yum install kernel-headers

安装的确实2.6.18-384......

	yum install kernel-devel-2.6.18-164.el5

提示没有这个包

	yum update kernel 好像也没提示成功?可能应该是升级一下.

重启之后就好了.变成高一点的版本了.








https://www.centos.org/modules/newbb/viewtopic.php?topic_id=30377
