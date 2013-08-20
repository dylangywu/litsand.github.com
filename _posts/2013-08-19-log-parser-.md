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

先在日志查看器中人工分析日志,发现登录产生一系列日志.决定采用528事件作为分析对象.528是2003用户登录成功事件.(之前的测试应该是登录不成功不会记录ip,登录成功是否记录ip要看连接协议)

使用命令

	C:\log>logparser.exe -i:evt -o:xls "select * into sec.xls from sec.evtx where EventID=528"
	

或得安全事件中id为528的事件,并且转化为csv格式.下一步就要过滤出来用户名和密码以及时间.

使用

	C:\log>LogParser.exe -i:csv -o:csv "select extract_token( Strings, 13, '|') as ipaddress into ipaddr.csv from sec.xls"

获得事件中的ip


	C:\log>LogParser.exe -i:csv -o:csv "select ipaddress, count(*) as hits into count.csv from ipaddr.csv group by ipaddress order by hits desc"
	

去得ip出现的次数,排序.

这样子得到了所有登录过的ip以及相应的次数.没有考虑到用户名.算是完成了60%吧.

## 参考资料 ##

[如何快速查找感染 Conficker 的客户端](http://blogs.technet.com/b/gcrsec/archive/2009/04/23/conficker.aspx)

[Log Parser-記錄檔分析器](http://blog.weithenn.org/2009/05/windowslog-parser.html)

[logparser及常用使用方法总结](http://www.cnblogs.com/qiqi9039420/articles/2051059.html)

[記錄剖析器 2.2 ](http://support.microsoft.com/kb/910447/zh-tw)



