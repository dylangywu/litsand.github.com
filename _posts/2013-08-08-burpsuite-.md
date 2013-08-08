---
layout: post
title: "burpsuite 使用记录"
description: ""
category: 
tags: []
---
{% include JB/setup %}


测试的时候用intruder显示starting，一直没有数据显示。注意到alert标签显示time out。怀疑是不是服务端有什么防护，把请求屏蔽掉了。可是用repeat可以正常发送接受数据。实验一下，把useragent改成google可以正常使用intruder。。。看来果然是useragent的关系。
