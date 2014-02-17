---
layout: post
title: "A dummies guide to SIEM"
description: ""
category: 
tags: []
---
{% include JB/setup %}


#security information & event management(siem)

## 概述

SIEM是一种为企业的信息的安全提供全面评估的安全管理方式。它把SIM（security information management）和SEM(security event management)整合进了一个统一的安全管理系统。

SEM和SIM基本功能的区别
<table>
    <tr>
        <td>Security Information  Event Management (SIEM)</td>
    </tr>
    <tr>
        <td>Security Event Management(SEM)</td><td>Security Information management(SIM)</td>
    </tr>
    <tr><td>Event Management </td><td>Centralized Log Collection</td></tr>
    <tr><td>Real-time Threat Analysis</td><td>Long term Log Storage</td></tr>
    <tr><td>Incident detection & Response</td><td>Log Search  Reporting</td></tr>
    <tr><td>Basic Ticketing capabilities</td><td></td></tr>
    <tr><td>Security Operations</td><td></td></tr>
</table>

	| Security Information & Event Management (SIEM) ||
	|Security Event Management(SEM)| Security Information management(SIM)| 
	|Event Management   |Centralized Log Collection|
	|Real-time Threat Analysis|Long term Log Storage| 
	|Incident detection & Response|Log Search & Reporting|
	|Basic Ticketing capabilities|| 
	|Security Operations||


## 为什么使用SIEM

安全需求：

1,SIEM是深层次的防护战略的核心

2,每一个攻击者都会留下一些踪迹，日志！

3,安全事件(event)可以对安全攻击事件提供深度的分析：

	什么时间：攻击时间戳
	发生了什么：是漏洞利用还是权限配置问题
	为什么会发生：帮助识别结构缺陷和问题修复

合规需求：

政策，安全，规范等要求安全监控，告警，报表和管理。（PCI,SOX,HIPAA,TRMG,ISO27K1）


##一次基本的攻击流程

1,攻击者扫描，试图寻找网络的漏洞

2,攻击者绕过防御，利用漏洞获取web服务器权限

3,通过web服务区，攻击者获得了数据库服务器上的敏感信息

4,攻击者在服务器上安装恶意软件，开启后门程序，下载重要数据。

![]({{site.img_url}}attacker1.png)

##怎样检测这次攻击

1,防火墙日志会有扫描相关的日志等

2,IDS/IPS日志会有检测到相关漏洞利用程序

3,web应用的日志包括access和inbound，outbound的流量

4,数据库日志

![]({{site.img_url}}attacker2.png)

##SIEM提供了全局的视野

![]({{site.img_url}}attacker3.png)


##SIEM应该具备的能力

1,日志收集能力，使用agent或者非agent的方式，方便快捷的收集各种IT资产日志。

2,解析和标准化，收集到的日志会通过解析成标准化的形式，方便存储，分析和生成报表。

3,关联不同形式的日志，从而识别出威胁。比如A事件紧紧挨着B事件出现，就采取某个措施。

4,实时的提醒和告警，在日志分析的基础上，对发现的安全威胁进行实时的告警。

5,安全事故检测和响应工作流程，跟踪检测到的安全事故和风险的工作流程。

