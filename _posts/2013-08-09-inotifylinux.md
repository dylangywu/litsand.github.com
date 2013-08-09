---
layout: post
title: "使用inotify监控linux文件系统"
description: ""
category: 
tags: [文件系统监控]
---
{% include JB/setup %}


## 参考资料 ##

[Inotify: 高效、实时的Linux文件系统事件监控框架](http://www.infoq.com/cn/articles/inotify-linux-file-system-event-monitoring#anch61060)

[Centos install inotifywait](http://blog.sina.com.cn/s/blog_6e2aeba301011260.html)

## 验证系统是否支持inotify ##

	grep INOTIFY_USER /boot/config-$(uname -r)

如果输出为CONFIG_INOTIFY_USER=y，说明内核支持inotify机制。

## centos 安装inotifywait ##

	wget http://cloud.github.com/downloads/rvoicilas/inotify-tools/inotify-tools-3.14.tar.gz

	tar -zxvf inotify-tools-3.14.tar.gz

	cd inotify-tools-3.14

	./configure

	make

	make install

	updatedb

	inotifywait -mrq -e modify,delete,create,attrib PATH
	-m 是保持一直监听
	-r 是递归查看目录
	-q 是打印出事件
	-e create,move,delete,modify,attrib 是指 “监听 创建 移动 删除 写入 权限” 事件
