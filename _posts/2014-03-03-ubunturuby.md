---
layout: post
title: "ubuntu升级ruby"
description: ""
category: 
tags: [ubuntu,ruby]
---
{% include JB/setup %}


ubuntu 13.10默认是ruby 1.9.3，看到ruby官网推荐2.1.1稳定版。就升级一下。编译安装。

[官网下载](https://www.ruby-lang.org/zh_cn/downloads/)

	wget http://cache.ruby-lang.org/pub/ruby/2.1/ruby-2.1.1.tar.gz

	./configure --prefix=/usr/local/ruby-2.1.1  
	配置安装路径

	make && make install 

	#设置PATH路径,把自己安装的ruby放在系统PATH前面，避免调用操作系统自带的ruby  
	export PATH=/usr/local/ruby-1.9.1/bin:$PATH  
	#我在 ~/.profile 文件中增加了这样的代码:  

	if [ -d "/usr/local/ruby-2.1.1/bin" ] ; then  
	    PATH="/usr/local/ruby-2.1.1/bin:$PATH"  
	fi  

	然后 注销 再登陆一次.  
	  
	#如无意外  
	  
	ruby -v  

	ruby 2.1.1p76 (2014-02-24 revision 45161) [x86_64-linux]


## 开机自动修改$PATH变量

在/etc/profile里 添加

	if [ -d "/usr/local/ruby-2.1.1/bin" ] ; then
	  PATH="/usr/local/ruby-2.1.1/bin:$PATH"
	fi

	echo $PATH >> /home/litdg/profile.txt

发现/home/litdg/profile.txt里变量是正常的，启动完成之后，新添加的

	/usr/local/ruby-2.1.1/bin

丢失。判断是之后执行的脚本覆盖了$PATH.


后来在.zshrc文件中发现了（系统使用的是zsh）直接定义$PATH变量的内容。

	export PATH="/usr/lib/lightdm/lightdm:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games"

在.zshrc文件末尾加 

	if [ -d "/usr/local/ruby-2.1.1/bin" ] ; then
	  PATH="/usr/local/ruby-2.1.1/bin:$PATH"
	fi

解决问题。