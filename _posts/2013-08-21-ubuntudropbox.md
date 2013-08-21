---
layout: post
title: "ubuntu安装dropbox"
description: ""
category: 工具使用
tags: [dropbox]
---
{% include JB/setup %}

ubuntu12.04

完整的过程如下:

在[官网](https://linux.dropbox.com/packages/ubuntu/)下载最新版的deb包.然后大家就都知道了,这个包是不完整的,打开会提示下载其他的东西,由于gfw的原因,是下载不了的.也就无法安装成功了.

装好deb包之后,下载这个包[tar](http://www.dropbox.com/download?plat=lnx.x86)

解压之后,放到家目录就可以了.注意这个包解压之后名字是以点开头的隐藏的文件名.需要用ls -a 查看.

## 遇到的问题 ##

之前按照在centos上安装的经验,直接下载[tar](http://www.dropbox.com/download?plat=lnx.x86)包解压.运行dropboxd提示connection close 之类的错误.

错误信息:

Did not receive a reply. Possible causes include: the remote application did not send a reply, the message bus security policy blocked the reply, the reply timeout expired, or the network connection was broken.

## 参考资料 ##

[https://www.dropbox.com/help/247/en](https://www.dropbox.com/help/247/en)

[http://blog.xuite.net/pacermania/a1253247/63262677-%5BUbuntu%5DDropbox%E5%AE%89%E8%A3%9D%2B%E4%BD%BF%E7%94%A8](http://blog.xuite.net/pacermania/a1253247/63262677-%5BUbuntu%5DDropbox%E5%AE%89%E8%A3%9D%2B%E4%BD%BF%E7%94%A8)


