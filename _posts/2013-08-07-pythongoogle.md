---
layout: post
title: "python获取google的搜索结果"
description: ""
category: 自动化工具
tags: [python,google]
---
{% include JB/setup %}

## google 搜索表单 ##
抓包网站备份文件扫描工具3.6获取其抓取google搜索结果的请求为
	GET /search?q=%E9%A3%9E%E6%9C%BA%E6%9D%AF&num=50&hl=zh-CN&safe=strict&prmd=fd&ei=d3GgTLmnNo_uvQP5_dyNDQ&start=0

谷歌搜索表单参数url[参数详解](http://ylbook.com/cms/web/gugecanshu.htm)

需要注意的地方是google最多获取1000条搜索结果，每次请求最多可以获取100条搜索结果。默认是10条。

查了一下资料发现还有两个api可以获取google的搜索结果.

	https://ajax.googleapis.com/ajax/services/search/web?v=1.0&q=
	https://www.googleapis.com/customsearch/v1?key=

无奈的是api获取到的结果跟网页获取的不完全一样.我需要测试的案例来说.本来有300条结果.api返回只有不到10条.没办法,只能用网页获取了.

这几个api的一些参考资料

https://developers.google.com/custom-search/v1/using_rest?hl=zh-CN#query-params
## python实现 ##

写成一个函数，参数为起始条目，以及这次请求获取的条目数。

