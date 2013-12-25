---
layout: post
title: "XSS, Command and SQL Injection vectors: Beyond the Form"
description: ""
category: 
tags: []
---
{% include JB/setup %}

这不是一篇介绍介绍技术的文章，只是一种开放思维的尝试。主要着重于当遇到输入过滤的情况下，如何找到可以利用的输入点。


![input]({{site.img_url}}.input1.png)


本质上来说如果要进行xss攻击，只要找到一个未过滤的输入点。输入的数据在支持脚本的软件（不仅限于浏览器）里解析就可以了。sqli也是类似的道理。而输入点并不仅仅是网页里的表单，只要去发现，输入点无处不在。下面介绍一些抛砖引玉的想法，也欢迎大家分享自己的思路。

#user agent 字段

![input]({{site.img_url}}.input2.png)

不仅仅是useragent字段，还包括头部的其他字段。比如把useragent改成

	"<script>window.location = "http://www.freebuf.com/"</script>" 

很多日志分析工具都是生成html格式的报告，如果这个字段没有合适的过滤编码就输出在html里，管理员查看的时候就会执行插入的js了。



#SSIDs

![input]({{site.img_url}}.input3.png)

这只是一个假想的攻击方式，SSIDs可以有32个字符甚至看可以包含不能打印的ASCII，SSID可能出现在某些记录wifi连接记录的日志里。


#事件日志

![input]({{site.img_url}}.input4.png)

很多人分析日志都会用第三方的日志查看工具。如果我们用


	<script>alert("XSS");</script>

作为用户名尝试登陆，登陆失败后会记录在日志中。第三方工具如果没有合适的过滤编码字符串，管理员查看工具生成的html格式的报告时就可能受到xss攻击。


#计算机名和描述

![input]({{site.img_url}}.input5.png)


这个场景我在Softperfect's NetScan实践过。在这里插入xss代码，当渗透测试人员查看扫描报告的时候。。。。。

![input]({{site.img_url}}.input6.png)

#应用的名字和元数据

![input]({{site.img_url}}.input7.png)

一些管理软件会列出来电脑上安装的软件信息，如果是用html来实现。。。

#Banners

大多网络扫描器都可以识别banner，而且可以输出多种格式，html，xml等。



