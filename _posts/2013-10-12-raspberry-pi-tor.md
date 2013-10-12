---
layout: post
title: "raspberry pi 搭建tor网络"
description: ""
category: 技术研究
tags: [raspberry,tor]
---
{% include JB/setup %}

https://www.idontplaydarts.com/2013/01/raspberry-pi-and-tor-for-slightly-easier-opsec/

为了更好的保护自己我在树莓派上安装了一个tor客户端.笔记本通过树莓派上网,把树莓派设置成所有的流量通过tor网络传输.这个方案的成本不超过50美金,但是可以极大的降低真实ip地址泄漏的风险.

即使笔记本被入侵控制了,这样一个物理隔离的硬件设备也可以保护你的身份不被识别出来.(当然,你的笔记本上不能保存着自己的身份信息)

## 软件安装 ##

即使对熟悉linux的人来说,设置树莓派也是一个漫长的过程.我选择了"Raspbian"这个基于Debian的系统.然后就是安装一系列软件.

sshd-管理树莓派用,监听eth0

iptables-控制设备的访问

macchanger-连接互联网的网卡随机生成mac地址

wireless-tools and wpa_supplicant-连接无线网

因为树莓派只有一个以太网卡所以我们需要一个额外的usb以太网卡或者usb无线网卡.我用的是[Alpha Card](http://www.amazon.com/Alfa-Wireless-Original-Screw-On-9dBi/dp/B001O9X9EU),它的兼容性比较好.

Tor和Polipo(一个代理程序)会占用比较多的内存,所以选用B型号的树莓派(512内存)性能会好一点.

整个网络的拓扑如下图:

![network]({{site.img_url}}berry.png)

如果需要socks方式代理,可以在eth0上开放Tor socks proxy的端口(TCP/9050)

## 系统设置 ##

树莓派的所以服务都配置成不保存日志.此外SSH设置成keys密钥对登录的方式.私钥不要保存在笔记本上!硬盘进行加密.

## 电源 ##

树莓派通过笔记本的USB供电,所以便携性非常好.树莓派启动的过程大概需要一分钟.


## 总结 ##

这个设备可以比较好的保护你在网络上的匿名性.如果是通过免费的wifi连接上互联网,那么别人识别你的身份会更加困难.

## 扩展阅读 ##

[Onion Pi](http://learn.adafruit.com/onion-pi/overview)


