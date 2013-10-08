---
layout: post
title: "vminjector绕过虚拟机认证"
description: ""
category: 工具使用
tags: [vmware,windows]
---
{% include JB/setup %}

https://github.com/batistam/VMInjector/tree/master/vminjector-src

http://www.secforce.com/blog/2012/11/vminjector/



xp 32bit 和win7 64bit使用python脚本都遇到了权限的问题.貌似是psutil不够给力?

而且vmware station v7 v8 都测试失败了.注入dll的时候失败.

!!!!!!!!!!!!!!!

VMInjector是一个绕过VMware Wordstation/Player上虚拟机登录认证的工具.支持当前大部分主流操作系统.

它的原理是直接操作内存的方式来绕过登录认证.所以这种内存补丁的方式是不持久的,虚拟机重启之后会恢复正常的密码验证功能.

利用条件:

1, 宿主机需要是windows(有管理员权限)

2, 虚拟化软件是VMware workstation或者player

3, 存在锁定的虚拟机

支持的版本:

宿主机支持32位和64位的windows,虚拟机支持win7,xin xp,MAC OS X,ubuntu大部分版本.

在64位win7主机,vmware 7.1.0,32位xp虚拟机测试通过

下载地址:https://github.com/batistam/VMInjector

测试心得:

github提供的源码中是用python脚本来注入DLL,我用python2.7.5,psutil模块1.1最新版,遍历主机进程的时候存在权限不够的问题.(貌似是psutil模块问题?).而且测试了多次都是注入失败.后来发现作者博客里演示的时候用的是编译好的exe.这两个exe在github上也有,只是后来被作者删掉了.不知道为什么.

所以

	git clone https://github.com/batistam/VMInjector
	cd VMInjector
	git rebase -i HEAD~2 (在编辑界面删除第2行文字!!!)

目的是撤销最近的一次提交.这样被删掉的exe文件就回来了.

	root@bt:~/VMInjector# ls
	LICENSE  README  vminjector  vminjector-src
	root@bt:~/VMInjector# git rebase -i HEAD~2
	Successfully rebased and updated refs/heads/master.
	root@bt:~/VMInjector# 
	root@bt:~/VMInjector# 
	root@bt:~/VMInjector# ls
	LICENSE  vminjector            vminjector64-exe.rar
	README   vminjector32-exe.rar  vminjector-src

使用方法命令行下执行,选择需要解锁的虚拟机然后选择操作系统版本.So easy!
	

