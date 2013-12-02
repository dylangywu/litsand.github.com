---
layout: post
title: "jsonp与js hijacking"
description: ""
category: 
tags: []
---
{% include JB/setup %}

http://firstleaf.diandian.com/post/2013-03-18/40049583114

http://drops.wooyun.org/papers/42

之前在写cors跨域资源共享的时候看到jsonp，然后在csrf蠕虫中看到callback劫持返回的数据，一直没怎么搞的很清楚状况。今天刚好看到两篇很不错的资料。总结记录一下。

js-hijacking其实是对jsonp的一种合法使用。是因为服务端验证不全导致的信息泄露。


