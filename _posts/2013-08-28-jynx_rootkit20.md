---
layout: post
title: "Jynx_Rootkit/2.0安装测试"
description: ""
category: 渗透测试
tags: [linux,rootkit]
---
{% include JB/setup %}



## 安装 ##

下载地址:

	wget http://blackhatlibrary.net/releases/jynx2.tgz
	tar zxvf jynx2.tgz

配置config.h


## 尝试过程 ##
直接centos 5.4 final make && make install 机器重启都起不来了...

# 测试1 #

	CentOS release 5.4 (Final)
	2.6.18-164.el5 X86_64

一开始编译出错,找不到一些文件.error: openssl/ssl.h: No such file or directory...

	yum install curl-devel expat-devel gettext-devel openssl-devel zlib-devel

搞定!





	[root@localhost jynx2]# make install
	[-] Initiating Installation Directory /XxJynx
	[-] Installing jynx2.so and reality.so
	[-] Morphing Magic GID (7)
	[-] Injecting jynx2.so
	make: *** [install] Segmentation fault
	[root@localhost jynx2]# ls
	Segmentation fault
	[root@localhost jynx2]# dir
	Segmentation fault


然后机器就挂了,重启都起不来了....

	ubuntu 10.04

	ubuntu 上apt-get install libssl-dev  解决找不到头文件的问题.

重启apache服务的时候起不来...

	rm: cannot remove `/var/run/apache2/*ssl_scache*'
	/usr/sbin/apache2ctl: line 93: 16120 Segmentation fault      $HTTPD ${APACHE_ARGUMENTS} -k $ARGV




## 参考资料 ##

http://www.blackhatlibrary.net/Jynx_Rootkit/2.0

http://resources.infosecinstitute.com/jynx2-sneak-peek-analysis/

http://blog.csdn.net/haoel/article/details/1602108

http://stackoverflow.com/questions/426230/what-is-the-ld-preload-trick

http://www.infosecisland.com/blogview/22440-Analyzing-Jynx-and-LDPRELOAD-Based-Rootkits.html

http://www.chokepoint.net/2013/08/jynxjynx2-userland-rootkits.html

