---
layout: post
title: "Tilon SpyEye2 Banking Trojan"
description: ""
category: 
tags: []
---
{% include JB/setup %}

![](http://4.bp.blogspot.com/-zfn-WfgXG8s/Uw7inPvzGXI/AAAAAAAAAHQ/80v2pLnpzp0/s1600/spyeye2-tilon-banking-malware.png)

当我们遇到各种恶意软件，漏洞利用工具，僵尸网络的时候，总是会想找一个有效的反病毒技术方案。但是最有效的方案总是抓捕恶意软件的作者和相关犯罪团伙的首脑。

Tilon是2012年出现的一个活跃的恶意软件家族。它的功能就是窃取感染用户的网上银行里的钱。安全研究员经过分析发现，最新版的变种Silon，就是SpyEye2银行木马。EpyEye2是SpyEye木马的升级版。SpyEye银行木马是24岁的俄罗斯黑客Aleksandr Andreevich Panin开发的(还有一个称呼Gribodemon）。他在2013年7月被警方逮捕。

SpyEye从2009年开始感染超过140万电脑。这个木马会窃取用户身份和金钱相关的信息，包括网上银行的认证信息，信用卡信息，用户名，密码，PIN码等。攻击者可以通过C&C服务器控制感染用户的电脑。通过多种手段，包括WEB代码注入，键盘记录等来窃取敏感信息。

![](http://1.bp.blogspot.com/-DY-IJWICKks/Uw7i4J2nevI/AAAAAAAAAHY/IqJZV7A5WXs/s1600/spyeye2-tilon-banking-malware.png)

安全研究员经过[仔细分析](http://foxitsecurity.files.wordpress.com/2014/02/spyeye2_tilon_20140225.pdf)确认开发SpyEye的团队跟开发Tilon的是同一伙人，所以Tilon被称为SpyEye2。比较有意思的是SpyEye2会检测被感染机器上是否安装了SpyEye，如果安装了的话，会主动卸载掉这个早期的不稳定版本。但是并不会检测任何别的恶意软件是否存在。另外一个认为Tilon就是SpyEye的变种的理由就是它太成功了。从12年开始就很活跃。但是去年SpyEye的作者被捕，Tilon也突然停止开发了。

FOX-IT的研究员说，抓捕像Gribodemon这样在地下经济中举足轻重的人物对于减少全球范围的网络犯罪活动很有意义。虽然这款恶意软件在将来依然会有人使用，但是随着使用量的慢慢下滑，它终会走到自己生命的尽头。