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
