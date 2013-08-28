---
layout: post
title: "redhat centos 手动配置IP"
description: ""
category: 工具使用
tags: [centos,redhat]
---
{% include JB/setup %}

## 配置文件 ##

	/ect/hosts 配置主机名和IP地址的对应
	/etc/sysconfig/network 配置主机名和网关
	/etc/sysconfig/network-scripts/ifcfg-eth0 eth0配置文件，eth1则文件名为ifcfg-eth1，以此类推


IP配置

假设我们要配置主机名为test，eth0的IP地址192.168.168.1/24，网关地址192.168.168.250

则/etc/sysconfig/network文件内容如下：

    NETWORKING=yes
    HOSTNAME=test
    GATEWAY=192.168.168.250

eth0对应的配置文件/etc/sysconfig/network-scripts/ifcfg-eth0内容如下：

    DEVICE=eth0
    BOOTPROTO=static
    IPADDR=192.168.168.1
    NETMASK=255.255.255.0
    TYPE=Ethernet
    ONBOOT=yes



DNS配置

修改/etc/reslov.conf

	nameserver 8.8.8.8


## 参考资料 ##

[Redhat Linux网卡配置与绑定](http://www.ningoo.net/html/2007/redhat_linux_network_configuration_and_bond.html)

