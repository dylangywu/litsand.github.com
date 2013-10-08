---
layout: post
title: "win7下psutil access denied的问题"
description: ""
category: 技术研究
tags: [python,psutil]
---
{% include JB/setup %}

## 参考资料 ##

http://www.blog.pythonlibrary.org/2010/10/03/how-to-find-and-list-all-running-processes-with-python/

https://groups.google.com/forum/#!topic/psutil/7Iv3L6GPeaI


Note: While this last script appears to work just fine on Windows XP, on Windows 7 32 and 64-bit, you will get an “Access Denied” traceback, I suspect this is caused by Window 7′s increased security, but I will try to find a workaround.

UPDATE (10/09/2010) – The psutil folks don’t know why it doesn’t work, but one of their developers has confirmed the issue. You can follow along on their Google Groups list.

## 测试环境 ##

win7  64bit

python 2.7.5 最新

psutil 1.1  最新


遍历进程的时候,对smss.exe(system)访问拒绝.还有几个local service用户的进程.采用administrator和system运行cmd,都存在这个问题.

