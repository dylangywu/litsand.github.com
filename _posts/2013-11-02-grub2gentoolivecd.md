---
layout: post
title: "使用grub2启动gentoolivecd"
description: ""
category: 
tags: [gurb2]
---
{% include JB/setup %}
# 参考资料

https://bugs.gentoo.org/show_bug.cgi?id=294268

之前ubuntu论坛有一个讲用grub引导iso安装ubuntu的帖子,现在发现不知道什么原因删掉了...最会会附上之前记录的一下关键步骤.

引导gentoo mini iso使用如下的配置:


	menuentry "Gentoo Linux minimal install cd x86" {
    loopback loop (hd0,3)/home/mad/install-x86-minimal.iso
    linux (loop)/isolinux/gentoo root=/dev/ram0 init=/linuxrc dokeymap looptype=squashfs loop=/image.squashfs cdroot initrd=gentoo.igz vga=791 isoboot=/home/mad/install-x86-minimal.iso
    initrd (loop)/isolinux/gentoo.igz
	}


livecd需要稍微修改一下下,我是根据livecd iso的结构对比了一下,发现livecd里多了一个boot目录

所以需要修改一下目录即可:

	menuentry "Gentoo Linux minimal install cd x86" {
    loopback loop (hd0,3)/home/mad/install-x86-minimal.iso
    linux (loop)/boot/gentoo64 root=/dev/ram0 init=/linuxrc dokeymap looptype=squashfs loop=/image.squashfs cdroot initrd=gentoo64.igz vga=791 isoboot=/home/mad/livecd.iso
    initrd (loop)/boot/gentoo64.igz
	}




	====== grub2 ======


	可以装各种系统完了~~~~~


	参考http://forum.ubuntu.org.cn/viewtopic.php?f=77&t=348452

	一开始把硬盘分区的写法搞错了，第一块硬盘的第一个分区应该是**（hd0,1)**,搞成了（hd0,0）。

	本次安装时把iso文件放在fat32分区，因为一开始因为是分区的格式问题导致找不到文件，事实上是因为分区号写错了。一会测试一下直接引导ntfs分区的iso文件。


	如果你的系统已经存在其他版本的linux

	首先判断你的grub的版本（启动时，grub显示1.98和1.99的是grub2,否则是grub1）,grub1也是修改menu.lst（/boot/grub/menu.lst）

	文件，写法同上。grub2则有区别，方法如下：

	1,grub2不用提取那两个文件。
	2,grub2建议把iso文件改名为ubuntu.iso，然后放到不用格式化的分区根目录下。
	3,编辑/etc/grub.d/40_custom，加上以下内容：
	（红色的X请自行修改，比如(hd0,2) 表示第一块硬盘的第二个分区，主分区用1、2、3、4,逻辑分区则从5开始。）
	引用:

	menuentry "Boot ubuntu 11.10 desktop iso" {
	insmod loopback
	insmod iso9660
	loopback loop (hd0,X)/ubuntu.iso
	linux (loop)/casper/vmlinuz boot=casper noacpi iso-scan/filename=/ubuntu.iso ro quiet splash --
	initrd (loop)/casper/initrd.lz
	}

	然后：

	代码:

	sudo update-grub


	重启即可。
	安装前，首先打开终端输入：

	代码:

	sudo umount -l /isodevice


	（此命令一定要运行，否则会出现不能对磁盘操作的情况！）
	其他同上面“七”、“八”两步。

	====== 关于grub2自定义配置文件40_custom的说明 ======


	所以对40_custom的文件编辑需要紧挨着注释写，这个条件有点苛刻。文档中的注释说的不是很明确。

	问题：
	1. Be careful not to change the 'exec tail' line above.

	40_custom 文件如下所示。不要去改动前面两行，因为'exec tail -n +3 $0'，已经暗示我们这个文件会从第三行开始读取有效配置。这点可以从update-grub2后生成的grub.cfg 中得到印证。所以如果你想要改任何的东东，哪怕是加注释或者空行。需要去更新正确的行数到-n后面。

	#!/bin/sh
	exec tail -n +3 $0  
	# This file provides an easy way to add custom menu entries.  Simply type the
	# menu entries you want to add after this comment.  Be careful not to change
	# the 'exec tail' line above.

	比如，有人希望在update-grub2时候看到有处理40_custom的动作，在'exec tail -n +3 $0'前面加入了
	echo “processing the 40_custom\r” 1>2&
	那就把你的+3改成+4就ok了，就像下面那样。

	#!/bin/sh
	echo "processing 40_custom\r" 1>&2
	exec tail -n +4 $0 
	# This file provides an easy way to add custom menu entries.  Simply type the
	# menu entries you want to add after this comment.  Be careful not to change
	# the 'exec tail' line above.

	所以，不要只看注释写的，说不动'exec tail‘那句话就ok，你不动那句，动了它周边的位置，也会有问题哦～～

	2. Simply type the menu entries you want to add after this comment.
	把要添加的menuentry直接紧跟着40_custom的注释之后，不要添加空行。否则会在grub.cfg 里也产生空行，这样启动的时候Grub2读grub.cfg 产生启动选项的时候，会不显示你添加的东东。
	正确定制如下，
	#!/bin/sh
	echo "processing 40_custom\r" 1>&2
	exec tail -n +4 $0 
	# This file provides an easy way to add custom menu entries.  Simply type the
	# menu entries you want to add after this comment.  Be careful not to change
	# the 'exec tail' line above.
	menuentry "Clonezilla live" {
    ...
    your customized boot entry
    ...
	}
