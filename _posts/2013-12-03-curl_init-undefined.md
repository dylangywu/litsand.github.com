---
layout: post
title: "curl_init undefined"
description: ""
category: 
tags: []
---
{% include JB/setup %}


测试一个php的exp，提示curl_init undefined。back track 5和kali都没有这个扩展，一开始想复杂了。以为需要自己安装，调整配置。

结果

	apt-get install php5-curl

就可以了。包管理器真实牛逼啊。简单易用的工具才是好工具。


http://www.queryinn.com/index.php/2010/03/fatal-error-call-to-undefined-function-curl_init-windows-and-linux-ubuntu/


