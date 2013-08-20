---
layout: post
title: "log parser 使用学习"
description: ""
category: 工具使用
tags: [日志分析,log]
---
{% include JB/setup %}


## log parser相关工具下载 ##

[log parser下载](http://www.microsoft.com/en-us/download/details.aspx?id=24659)

[log parser lizard](http://www.lizard-labs.net/log_parser_lizard.aspx)

## 实战案例 ##

目标:在windows的安全事件中找出用户admin登录的ip来源.并按照ip出现的次数排序.

操作过程:

evt格式的日志log parser2.2提示格式错误,用高版本的日志查看器打开另存为evtx格式就可以正常识别了.

先在日志查看器中人工分析日志,发现登录产生的

