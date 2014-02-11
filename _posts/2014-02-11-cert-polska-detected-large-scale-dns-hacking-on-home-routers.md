---
layout: post
title: "CERT POLSKA detected large scale DNS hacking on home routers"
description: ""
category: 
tags: []
---
{% include JB/setup %}

波兰计算机应急中心检测到大量家用路由器的DNS配置被修改。黑客对大量网上银行的用户实施了中间人攻击。

波兰CERT在[公告](https://www.cert.pl/news/8019/langswitch_lang/en)中写到:
	”很多家用路由器存在未授权的远程修改配置漏洞导致了这次事件。这次中间人攻击中，黑客在多个网上银行的页面中注入了恶意的javascript代码欺骗用户输入账号密码和交易确认码。最后窃取了用户银行里的钱“
攻击的流程原理可用下图表示：

！[pic]({{site.img_url}}dnspolar2.png)

为了绕过HTTPS，黑客采用了SSL剥离的技术。攻击者阻止用户和银行之间建立SSL连接，使用户和代理服务器之间使用未加密的http通信。

唯一不同的是浏览器会提醒用户连接没有使用SSL加密。攻击者为了迷惑用户，重写了url，在域名前加了个“ssl-."的前缀。当然这个域名是不存在的，只能在黑客的恶意DNS才能解析。

![pic]({{site.img_url}}dnsploar.png)

更多关于SSL stripping的技术可以参考[利用sslstrip和ettercap突破ssl嗅探密码](http://www.freebuf.com/articles/web/5929.html)

根据波兰IT安全组织Niebezpiecznik.pl的调查，攻击者很可能利用的是ZyNOS路由器固件的漏洞，利用这个漏洞，攻击者可以未授权的情况下下载到路由器的配置信息，从而获取路由器的登陆密码。

波兰CERT的负责人Przemyslaw Jaroszewski提到，修改家用路由器的DNS记录有很多方法，有些已经存在很多年了。这次让我们吃惊的是第一次有如此大规模的，目的是窃取金钱的利用。这种针对网上银行用户的DNS劫持也许会越来越多。

防御的建议是家用路由器不要开启任何的远程管理功能，不要使用默认的管理密码。去年国内也曾爆发过一次大规模的家用路由器DNS劫持攻击。相关的技术和防御方法可以参考[说说“史上最大规模的DNS劫持”](http://www.freebuf.com/articles/web/9723.html)