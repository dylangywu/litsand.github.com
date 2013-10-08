---
layout: post
title: "本地cmd获取system权限"
description: ""
category: 技术研究
tags: [cmd,system,win7]
---
{% include JB/setup %}

## 参考资料 ##

http://hi.baidu.com/darks00n/item/22212cc397faf627ee466528

win7 64位下测试:

at的方式提示:

	C:\Users\litsand>at 14:02 /interactive cmd
	警告: 由于有安全增强功能，此任务将在预期时间运行，但不以交互方式运行。
	如果要求交互式任务，则使用 schtasks.exe 实用程序(使用 "schtasks /?" 命令
	获取详细信息)。
	新加了一项作业，其作业 ID = 1

创建system权限cmd失败

采用创建服务的方法:

可以创建一个system权限cmd,不过兼容性貌似有问题,会在新的会话中创建.



xp下可以用at的方式成功创建system权限cmd

psexec可以成功获取system权限,不过命令的回显貌似有问题.
