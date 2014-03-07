---
layout: post
title: "salted password hashing doing it right"
description: ""
category: 
tags: []
---
{% include JB/setup %}

大多数的web开发者都会遇到设计用户账号系统的需求。账号系统最重要的一个方面就是如何保护用户的密码。一些大公司的用户数据库泄露事件也时有发生，所以我们必须采取一些措施来保护用户的密码，即使网站被攻破的情况下也不会造成较大的危害。保护密码最好的的方式就是使用带盐的密码hash(salted password hashing).对密码进行hash操作是一件很简单的事情，但是很多人都犯了错。接下来我希望可以详细的阐述如何恰当的对密码进行hash，以及为什么要这样做。

## 重要提醒

如果你打算自己写一段代码来进行密码hash，那么赶紧停下吧。这样太容易犯错了。这个提醒使用域每一个人，**不要自己写密码的hash算法** ！关于保存密码的问题已经有了成熟的方案，那就是使用[phpass](http://www.openwall.com/phpass/)或者本文提供的源码。

## 什么是hash

	hash("hello") = 2cf24dba5fb0a30e26e83b2ac5b9e29e1b161e5c1fa7425e73043362938b9824
	hash("hbllo") = 58756879c05c68dfac9866712fad6a93f8146f337a69afe7dd238f3364946366
	hash("waltz") = c0e81794384491161f1777c232bc6bd9ec38f616560b120fda8e90f383853542

Hash算法是一种单向的函数。它可以把任意数量的数据转换成固定长度的“指纹”，这个过程是不可逆的。而且只要输入发生改变，哪怕只有一个bit，输出的hash值也会有很大不同。这种特性恰好合适用来用来保存密码。因为我们希望使用一种不可逆的算法来加密保存的密码，同时又需要在用户登陆的时候验证密码是否正确。

在一个使用hash的账号系统中，用户注册和认证的大致流程如下：

1, 用户创建自己的账号
2, 用户密码经过hash操作之后存储在数据库中。没有任何明文的密码存储在服务器的硬盘上。
3, 用户登陆的时候，将用户输入的密码进行hash操作后与数据库里保存的密码hash值进行对比。
4, 如果hash值完全一样，则认为用户输入的密码是正确的。否则就认为用户输入了无效的密码。
5, 每次用户尝试登陆的时候就重复步骤3和步骤4。

在步骤4的时候不要告诉用户是账号还是密码错了。只需要显示一个通用的提示，比如账号或密码不正确就可以了。这样可以防止攻击者枚举有效的用户名。

还需要注意的是用来保护密码的hash函数跟数据结构课上见过的hash函数不完全一样。比如实现hash表的hash函数设计的目的是快速，但是不够安全。只有密码学hash函数(cryptographic hash functions)可以用来进行密码的hash。这样的函数有SHA256, SHA512, RipeMD, WHIRLPOOL等。






	Dictionary Attack

	Trying apple        : failed
	Trying blueberry    : failed
	Trying justinbeiber : failed
	...
	Trying letmein      : failed
	Trying s3cr3t       : success!

	
	Brute Force Attack

	Trying aaaa : failed
	Trying aaab : failed
	Trying aaac : failed
	...
	Trying acdb : failed
	Trying acdc : success!