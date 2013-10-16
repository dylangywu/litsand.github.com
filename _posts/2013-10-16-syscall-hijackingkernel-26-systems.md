---
layout: post
title: "syscall hijacking:kernel 2.6 systems"
description: ""
category: 技术研究
tags: [kernel]
---
{% include JB/setup %}

这篇文章会详细的介绍如何劫持系统调用.文章中讨论的重点是如何绕过内核的保护和cpu的"保护模式".

## 访问系统调用表 ##

内核版本2.4以前的rootkit在2.6及以上的内核里是无法运行的.这是因为2.6以后,"sys_call_table"无法导出了,也就没有办法直接访问到.而且"sys_call_table"所在的内存页具有写保护,下面这种方式也是没法访问的:


	extern void *sys_call_table[];
	...
	sys_call_table[__NR_syscall] = pointer

不过这个表终归是在内存里,只要我们知道了它的内存地址,就可以通过一个简单的指针直接访问.有很多种办法可以获取到这个地址,最简单的就是查找"/boot"目录下的"System.map"文件了.这个文件是内核编译的时候创建的,它包含了内核使用的所有代号和想对应的地址.

这个文件的内容大致如下:

	spaccio@spaccio-laptop:~$ cat /boot/System.map-2.6.35-23-generic
	...
	c018d140 t cgroup_remount
	c018d260 T cgroup_path
	c018d310 t allocate_cg_links
	c018d410 t find_css_set
	c018d7d0 T cgroup_attach_task
	c018da40 T cgroup_clone
	c018dcc0 t cgroup_tasks_write
	c018dd90 t cgroup_release_agent
	c018df50 t proc_cgroup_show
	c018e180 t cgroup_pidlist_find
	c018e320 t cgroup_write_event_control
	c018e610 t pidlist_allocate
	c018e640 t pidlist_array_load
	...


内容很多,不过我们只对"sys_call_table"的内容感兴趣:

	spaccio@spaccio-laptop:~$ cat /boot/System.map-2.6.35-23-generic | grep sys_call_table
	c05d2180 R sys_call_table

## 绕过内核的写保护 ##

现在我们得到了系统调用表的地址,不过如果大家细心的话会发现"grep"命令的输出中有一个"R"的标志,它代表了这个地址是"只读的".

事实上内核在这个只读的区域内存储了一些结构,这样子就可以防止这些结构被修改而导致系统的不稳定.所以我们想要修改这些结构的话首先要把它设置成"读写"模式.

幸运的是内核已经给我们提供了一个特殊的函数来完成这个任务:

	void (*pages_rw)(struct page *page, int numpages) =  (void *) 0xc012fbb0;
	void (*pages_ro)(struct page *page, int numpages) =  (void *) 0xc012fe80;


