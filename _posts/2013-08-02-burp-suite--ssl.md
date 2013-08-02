---
layout: post
title: "burp suite 之 ssl证书"
description: ""
category: 
tags: []
---
{% include JB/setup %}

burp suite 代理https的时候出现证书的问题，导致无法代理监听数据。按照网上的方法导出证书，导入证书发现没有效果。而且想添加exception的时候出现已经存在有效的证书，导致exception都添加不了。。。google了下。[参照这篇文章](http://support.mozilla.org/zh-CN/questions/954621)

在历史记录里清空数据，删掉相关的证书，然后采用添加exception的方式临时解决这个问题。

