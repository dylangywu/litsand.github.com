---
layout: post
title: "a splunk big data case study"
description: ""
category: 
tags: []
---
{% include JB/setup %}


Santa Claus(圣诞老人)international,和它的其他国际国际品牌像Father Christmas,Kris Kringle,saint Nicholas 和Pere Noel 使用splunk处理大约18 Elfabytes的数据。(processing about 18 Elfabytes of data day in Splunk)意思是总共处理了这么多数据？应该不是每天。18Elfabytes是1024^8 bytes.1 gigabyte = 1024^3 = 1,073,741,824 bytes,这个数量难以置信啊，都数不不过来了。。。）他们使用splunk来提高用户的圣诞节体验，管理他们的礼物操作流程，确保礼物及时的送到20亿儿童手上。

下面讨论一下圣诞老人国际是如何利用splunk和大数据实现操作智能化的。

圣诞老人国际在商业上面临很多威胁，除了传统的企业竞争，


为了发展，圣诞老人国际也在寻求自身的差异化，调整一些衡量自身的KPI：

1,确保礼物送达的及时性。

2,准确寄送孩子圣诞礼物列表中的礼物

3,正确的预测淘气的和乖的孩子

4,礼物的生产，检验和包装的质量。

下图是圣诞老人国际（SCI）的生产车间。

![plunk]({{site.img_url}splunk1.jpg})


SCI有很多的数据来源，大部分数据可以进行管理和分析，下面是他们分析数据的几个例子：


IT操作和应用管理数据--SCI所有的日志文件（yule log）都在splunk上索引。包括ERP（Enhanced Reindeer Planning）系统，CRM（christmas Readiness Management)应用和NOC（Noel Operations Centre)。有超过20亿的孩子需要寄送礼物，平均每个孩子有3个礼物。SCI有很多的礼物处理数据，他们建立了自己的基于云的应用来处理。这些数据也在splunk中索引。

HoHoHoDoop--SCI自己定制了Hadoop，起了个名字叫HoHoHoDoop。这个集群存储了大概10Elfabytes的数据。包括所有他们寄送过礼物的孩子，礼物是何时收到的，过去收到的礼物等等。

安全信息--为了防止外部的网络攻击，SCI使用splunk实时监控DDOS攻击尝试和APTs（Awful Present Threats)攻击。

数据智能--2013年SCI启动了他们第一个线上服务，Giftlink，这个服务允许孩子通过多种方式提交他们的需求。splunk实时的监控这个服务的数据，还可以生成很多报表。过度的礼物请求，splunk会产生告警，提醒这是一个淘气的孩子。SCI还准备上线一个新的服务来交易自己不喜欢的礼物。偶尔，人们也会犯错，买了自己不喜欢的东西。

![plunk]({{site.img_url}splunk2.jpg})

右边的图显示英国打开的礼物数和关闭的数量对比。左边是北美已经送达的礼物数量和未送达的实时数据。
