---
layout: post
title: "virtualbox安装windows2012"
description: ""
category: 工具使用
tags: [virtualbox]
---
{% include JB/setup %}

virtualbox安装windows 8.1或者windows server 2012 r2，出现0x000000C4错误，解决方案如下：
（1）命令行下， 运行下面代码，找到VM名称，通常是大括号及其内部内容：

	"C:\Program Files\Oracle\VirtualBox\VBoxManage.exe"  list vms
	
（2）然后在命令行下运行下面的代码，记得先将[vmname]替换为VM名称：
	
	"C:\Program Files\Oracle\VirtualBox\VBoxManage.exe" setextradata [vmname] VBoxInternal/CPUM/CMPXCHG16B 1


序列号:


    数据中心版序列号：
    JGXYY-7NMTC-MHKY3-QCC9B-VQRG7
    自动虚拟机激活（AVMA）序列号：
    XVNRV-9HTX4-TH2JD-HVJQD-QRQWG
