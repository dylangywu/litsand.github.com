---
layout: post
title: "win2008 group policy preference"
description: ""
category: 
tags: [win2008]
---
{% include JB/setup %}


windows2008有一个叫组策略首选项(Group Policy Preference)的新特性.这个特性可以方便管理员在整个域内部署策略.本文会详细介绍这个组策略首选项的一些缺陷.尤其是当下发的策略包含用户名和认证信息的时候,一个普通的用户就可以通过这些信息或得策略里的帐号密码,从而提升自己的权限甚至控制域内其他计算机.


组策略首选项允许域管理员向域内的计算机推送各种策略.比如登录的时候自动映射网络硬盘,更新内建administrator帐号的密码,修改注册表,启动启动程序,新建用户等等.

更多如何创建部署策略的细节就不多说了.下面是一条更新administrator管理员帐号的策略.如下图,是一个xml的文件.
![img]({{site.img_url}}groupp1.png)

可以看到这条策略的内容是把administrator账户重命名为locadm.密码是cpassword字段,是加密过的.

当一条组策略首选项在域成员上部署之后,比如一台win7的机器部署了这条更新默认帐号的策略之后,会自动创建一个目录"C:\Users\All Users\Microsoft\Group Policy\History\".部署之后的策略xml文件就会保存在这里.这个例子中路径是这样的:

C:\Users\All Users\Microsoft\Group Policy\History\{A1C0C41B-D2F8-401B-A5D1-437DA197A809}\Machine\Preferences\Groups\Groups.xml

任何登录的用户对这个文件都有读权限:

![img]({{site.img_url}}groupp2.png)

关键的问题在于这个密码虽然是加密的,但是是可以破解的!它使用了256位的AES加密,这个32字节的key被微软写在[文档](http://msdn.microsoft.com/en-us/library/cc422924.aspx)里了.....

	The 32-byte AES key is as follows:
	4e 99 06 e8  fc b6 6c c9  fa f4 93 10  62 0f fe e8
	f4 96 e8 06  cc 05 79 90  20 9b 09 a4  33 b6 6c 1b

所以任何接触到这个文件的用户都可以轻易的破解出配置文件中的密码.

而组策略首选项一般是在较大的域内应用,更改默认密码这样的策略一般都是针对多台计算机下发的.所以获得了策略中的帐号可能在其他的计算机上同样有效.

组策略首选项的配置文件是通过SMB协议下发的,内容没有加密.这就导致可以监听流量或得配置信息,从而得到帐号密码.下图是从网络流量中获取xml配置文件的截图:

![img]({{site.img_url}}groupp3.png)

# 工具使用

[下载地址](http://carnal0wnage.attackresearch.com/2012/10/group-policy-preferences-and-getting.html)

![img]({{site.img_url}}groupp4.png)

工具比较简单,解密的字符串是写在程序里的:

![img]({{site.img_url}}groupp5.png)

