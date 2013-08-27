---
layout: post
title: "ubuntu报错:apt:cache"
description: ""
category: 工具使用
tags: [ubuntu,apt-get]
---
{% include JB/setup %}

ubuntu 10.04

报错信息:Dynamic MMap ran out of room. Please increase the size of APT::Cache-Limit. Current value: 25165824. (man 5 apt.conf)

参考[askubuntu](http://askubuntu.com/questions/219523/dynamic-mmap-ran-out-of-room-when-trying-to-sudo-apt-get-anything)

I believe one solution is just increase the value APT::Cache-Limit at the /etc/apt/apt.conf.d/70debconf, to do so use:

	sudo gedit /etc/apt/apt.conf.d/70debconf

	and add the following to the end of the file:

	APT::Cache-Limit "100000000";

	..and then run:

	sudo apt-get clean
	sudo apt-get update --fix-missing

