---
layout: post
title: "kali安装vbox tools"
description: ""
category: 工具使用
tags: [kali,vbox]
---
{% include JB/setup %}

kali 版本1.05.

装vbox tools的时候编译失败,看日志是没有linux-heades.执行

	apt-get update && apt-get install -y linux-headers-$(uname -r)
	

提示找不到 3.7-trunk-pae的内核.然后差资料发现可能是源的问题.默认source.lst只有security.kali.org一个源.

添加

	deb http://http.kali.org/kali kali main contrib non-free

后

	apt-get update

搞定.

##  ##
https://forums.kali.org/showthread.php?5804-Kernel-headers-problem
