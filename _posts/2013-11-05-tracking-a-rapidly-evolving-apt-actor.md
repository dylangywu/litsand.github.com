---
layout: post
title: "Tracking a rapidly evolving apt actor"
description: ""
category: 技术研究
tags: [apt]
---
{% include JB/setup %}

了解你的对手:追踪一个响应快速的APT攻击源头

10月24日到25日期间,我们检测到两起来自"admin@338"(根据攻击的特征,给攻击者起的代号)的鱼叉式网络钓鱼攻击.攻击目标针对多个组织,而且意图非常明显,是为了获取国际贸易,金融,经济政策等方面的资料.这两起攻击使用了不同的恶意软件并且表现出了快速调整技术,策略,流程(TTPs)的能力.


# 使用投资者指南和联系人列表作为诱饵

2013年10月25日.星期五.检测到对下列目标的攻击:


* 西欧中心银行
* 一个国际贸易,经济,金融政策领域的组织机构
* 一个美国的智囊团
* 远东一个国家的政府部门


钓鱼邮件如下图所示,恶意的word文档利用了CVE-2012-0158漏洞

![email]({{site.img_url}}aptphish1.jpg)

Word文档的属性:

* 文件名:Investor Relations Contacts.doc
* MD5: 875767086897e90fb47a021b45e161b2

当打开这个Word的时候会写一个可执行文件到C:\Windows\wmiserver.exe.然后运行这个程序,这是一个Bozok远控的变种.它连接的CnC服务器是 microsoft.mrbasic.com和www.microsoft.mrbasic.com.使用的连接密码是“wwwst@Admin”.我们发现10月26日的时候两个域名解析到了58.64.153.157.

# Bozok远控的能力

Bozok跟其他流行的远控一样,可以从网上自由的下载到.Bozok的作者绰号叫"Slayer616",他还有一款叫Schwarze sonne(简称SS-RAT)也非常有名.这两款远控在互联网上很容易就可以找到,在之前的攻击中也被广泛使用.

跟SS-RAT不一样,Bozok依然在维护中,10月份刚更新了两个版本,修复了一些bug,增加了对西班牙语,阿拉伯语,保加利亚语,波兰语和法语的支持.如下图所示,比较方便易用.

![email]({{site.img_url}}aptphish2.jpg)

一旦计算机被Bozok感染,攻击者就可以做很多未授权的事情:


* 下载被感染计算机的文件或上传文件
* 启动终止进程
* 修改注册表
* 抓取系统上保存的密码


还可以通过图形界面执行任意命令.


![email]({{site.img_url}}aptphish3.jpg)

攻击者还可以上传一个DLL插件来扩展Bozok的功能,包括开启摄像头,记录键盘操作,查看控制屏幕等.

Bozok在可执行文件的资源段保存了配置参数.这次攻击中获取到的Bozok变种(wmiserver.exe c5d8b7c8e2f50b171840e071f8a079b6)配置参数如下:


![email]({{site.img_url}}aptphish4.jpg)

	ID = aubok
	Mutex = 801JsYqFulHpg
	Filename = wmiserver.exe
	Startup Entry Name = wmiupdate
	Plugin filename = ext.dat
	Connection password = wwwst@Admin
	Connection port = 80
	Connection servers = www.microsoft.mrbasic.com, microsoft.mrbasic.com

在感染的时候,Bozok发出的初始化网络流量如下图:

![email]({{site.img_url}}aptphish5.jpg)

从抓包中分析除了远控的连接密码为"wwwst@Admin"

# 确认攻击源头

我们认为这次攻击来自于"admin@338",关于这个攻击者的特征,在<<Assessing Damage and Extracting Intelligence>>的报告中有详细的描述.admin@338在之前的攻击中也使用过"wwwst@Admin"作为密码.

此外,这次攻击中CnC服务器的IP是58.64.153.157.很多跟admin@338有关的CnC域名也都指向了这个IP.在10月27日,我们观察到已知的,指向这个ip的域名有:


	consilium.dnset.com
	consilium.dynssl.com
	consilium.proxydns.com
	dnscache.lookin.at
	ecnet.rr.nu
	european.athersite.com
	hq.dsmtp.com
	hq.dynssl.com
	ipsecupdate.byinter.net
	itagov.byinter.net
	microsoft.acmetoy.com
	microsoft.dhcp.biz
	microsoft.dynssl.com
	microsoft.ftpserver.biz
	microsoft.instanthq.com
	microsoft.isasecret.com
	microsoft.lookin.at
	microsoft.proxydns.com
	microsoft.wikaba.com
	microsofta.byinter.net
	microsoftb.byinter.net
	phpdns.myredirect.us
	sslupdate.byinter.net
	svchost.lookin.at
	svchost.passas.us
	teamware.rr.nu
	webserver.dynssl.com
	webserver.fartit.com
	webserver.freetcp.com
	www.consilium.dnset.com
	www.consilium.dynssl.com
	www.consilium.proxydns.com
	www.hq.dsmtp.com
	www.hq.dynssl.com
	www.microsoft.acmetoy.com
	www.microsoft.dhcp.biz
	www.microsoft.dsmtp.com
	www.microsoft.dynssl.com
	www.microsoft.instanthq.com
	www.microsoft.isasecret.com
	www.microsoft.proxydns.com
	www.microsoft.wikaba.com
	www.svchost.ddns.info
	www.svchost.dyndns.pro
	www.svchost.dynssl.com
	www.verizon.dynssl.com
	www.verizon.itemdb.com
	www.verizon.proxydns.com
	www.webserver.dynssl.com
	www.webserver.fartit.com
	www.webserver.freetcp.com

我们之前检测到admin@338在1月6日的攻击中也使用了Bozok,在那次攻击中,他使用邮件发送了一个恶意的"EcoMissionList.xls"的表格.攻击的目标跟这次一样,也是贸易,经济,金融政策等领域的组织机构.使用的CnC服务器是www.microsoftupdate.dynssl.com,密码是"gwxpass".


# 亚太地区投资人联系方式诱饵

我们分析最近用投资人联系方式做诱饵的Bozok样本,发现在10月24日有一起相似的攻击.就在这次攻击的前一天.攻击者使用了同样的方式给同一个美国智囊团发送了钓鱼邮件.

恶意的Word文档名为:Investor Relations Contacts-AsiaPacific.doc.释放一个svchost.exe(C:\Documents and Settings\admin\Application Data\svchost.exe).我们把这个文件归类为Backdoor.APT.FakeWinHTTPHelper.连接的CnC服务器为www.dpmc.dynssl.com 和www.dataupdate.dynssl.com

www.dpmc.dynssl.com和www.dataupdate.dynssl.com分别在10月24日和21日解析到了58.64.153.157.


#结论

这一系列连续的事件,从24日的Backdoor.APT.FakeWinHTTPHelper到25日的Bozok反映了admin@338具有快速调整技术,策略和流程(TTPs)的能力.而且,admin@338把公开获取到的远控(Poison Ivy,Bozok)和自己编写的远控(Backdoor.APT.FakeWinHTTPHelper)都整合进了自己的兵器库.

# 扩展阅读

[1] For more on admin@338, see our previous blog entry on Poision Ivy at  http://www.fireeye.com/blog/technical/targeted-attack/2013/08/pivy-assessing-damage-and-extracting-intel.html

[2] http://ss-rat.blogspot.com/

[3] This report is available at http://www.fireeye.com/resources/pdfs/fireeye-poison-ivy-report.pdf
