---
layout: post
title: "sed报错unknown option to s"
description: ""
category: 代码人生
tags: [bash]
---
{% include JB/setup %}

一段代码在centos下可以执行,ubuntu下执行报错,一开始怀疑是不是不同的版本sed问题.google了一下,找到一个很好的[资料](http://www.linuxmisc.com/10-unix-questions/8ffaf0e2525e6fde.htm)

可以看到大家认为引起这个错误的可能有几种不同的状况.我这里是采用bash pam.sh执行就不会报错了,看来是不同的shell对单双引号导致的.


Use double quotes, not single quotes. Shell variable substitution
(Bourne shell) is done inside double quotes, biut not inside single
quotes. Why does sed usually use single quotes, then? Because you
normally want sed to see the $ more often than you want the shell to see
it. "man sh" on quoting rules. 
