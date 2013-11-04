---
layout: post
title: "inter protocol communication测试"
description: ""
category: 技术研究
tags: [inter-protocol]
---
{% include JB/setup %}

测试了下inter-protocol communication.

http://www.secforce.com/blog/tag/inter-protocol-exploitation/

使用pure-ftpd测试成功!

不过默认的情况下浏览器会对21端口的请求拦截,不会发送21端口的请求.

在win2003上 ftp服务测试失败.

	200 Microsoft FTP Service
	500 "GET /favicon.ico HTTP/1.1": command not understood
	421 Timeout (120 seconds): closing control connection.
	421 Terminating connection.

会超时,可能对数据的处理有点问题.


