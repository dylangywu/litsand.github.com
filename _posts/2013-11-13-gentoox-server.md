---
layout: post
title: "gentoo安装X server"
description: ""
category: 
tags: [gentoo]
---
{% include JB/setup %}

第一次安装遇到的问题:

gentoo 安装X no screen found

百度搜索只有两条结果。看了一下Xorg的日志，差不多都是后面几个模块找不到。就按照提示：

	emerge xf86-video-vesa xf86-video-fbdev

一下，果然好了。等下熟悉了，多安装几遍。深究一下

这次安装参考的这里https://wiki.gentoo.org/wiki/X_server

步骤如下:

	emerge x11-base/xorg-drivers
	emerge media-libs/mesa
	emerge x11-base/xorg-server

然后就OK了.



