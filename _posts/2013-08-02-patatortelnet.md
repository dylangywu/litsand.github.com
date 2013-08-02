---
layout: post
title: "patator使用指南之破解telnet"
description: ""
category: 
tags: []
---
{% include JB/setup %}

[项目主页](http://code.google.com/p/patator/)

话说真的十分的强大和灵活。

## 破解telnet ##

无参数执行 python patator_v0.5.py 列出所有可用的模块。

加模块名字，列出该模块的主要参数和用法：

	python patator_v0.5.py telnet_login
	Patator v0.5 (http://code.google.com/p/patator/)
	Usage: telnet_login <module-options ...> [global-options ...]

	Examples:
	telnet_login host=10.0.0.1 inputs='FILE0\nFILE1' 0=logins.txt 1=passwords.txt persistent=0 prompt_re='Username:|Password:' -x ignore:egrep='Login incorrect.+Username:'

	Module options:
	host          : target host
	port          : target port [23]
	inputs        : list of values to input
	prompt_re     : regular expression to match prompts [\w+]
	timeout       : seconds to wait for a response and for prompt_re to match received data [20]
	persistent    : use persistent connections [1|0] 

	ERROR: wrong usage. Please read the README inside for more information.


prompt_re可以自定义提示输入用户名和密码的文字。ignort:egrep可以自定义密码错误时的回显。timeout定义超时，也很有用。

