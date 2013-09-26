---
layout: post
title: "ubuntu搭建openvpn server"
description: ""
category: 工具使用
tags: [openvpn,nat,ubuntu]
---
{% include JB/setup %}

测试环境为ubuntu 10.04

参考资料:

[10.04 openvpn](https://help.ubuntu.com/10.04/serverguide/openvpn.html)

[13.04 openvpn](https://help.ubuntu.com/13.04/serverguide/openvpn.html)

[linux配置nat](http://hi.baidu.com/saiafgdyshbbehq/item/07c19cae0b45db736dd45573)


## 安装过程 ##

参考10.04的安装 步骤完成安装.

然后修改了server.conf

	push "redirect-gateway def1 bypass-dhcp"
	push "dhcp-option DNS 10.0.0.2"
	push "dhcp-option DNS 10.1.0.2"

然后利用iptables做好nat

实验发现,连上之后过1分钟左右网络才同.

