---
layout: post
title: "phdays I Bank Pro"
description: ""
category: 
tags: []
---
{% include JB/setup %}

#对虚拟银行系统的渗透测试

虚拟机{{的登陆用户是root，密码是phd2012。配置好ip之后，就可以从浏览器访问这个虚拟银行系统了。

![ibank1]({{site.img_url}}ibank1.png)


可以看到这个登陆页面提供了用户名，密码，还有验证码的输入框。当然事先我们是不知道密码的，先用burpsuite的爬虫爬一下看有哪些暴露的url。

结果在意料之中，除了登陆，找回密码页面，没有其他的信息了。

![ibank1]({{site.img_url}}ibank2.png)

先看一下找回密码页面，提供了一个应该是输入用户名的input框。

![ibank1]({{site.img_url}}ibank3.png)

随便输入一个admin，点击enter。提示为Identifier not found。聪明的小伙伴们肯定已经想到了。这是提示用户名不存在呀，很可能可以暴力猜解用户名。根据老外的介绍，Identifier代表这个字段应该是数字，进一步降低了暴力猜解的成本。当然这个地方经过测试，确实是可以猜解用户名的。不过为了说明验证码设计的一些问题，本文采用绕过登陆页面的验证码来暴力猜解。

![ibank1]({{site.img_url}}ibank4.png)


先看一下登陆页面验证码部分的源码。可以看到验证码是通过image.php结合code参数生成的。而且表单中还有一个隐藏的字段，值跟验证码的生成连接中是一样的。每生成一次验证码，这个值都会变化。显然是跟验证码密切相关的，这个字符串看起来很像是base64编码过的。


![ibank1]({{site.img_url}}ibank6.png)

先用burpsuite的decoder解码试试看。

![ibank1]({{site.img_url}}ibank7.png)

解码的结果是一个看起来好像无意义的字符串"=IjN5YTO"，有一点小纠结，还以为解码出来直接就是验证码呢。但是我们注意到这个字符串以”=“开头，我们最熟悉的base64特征不就是以”=“结尾么，把这个字符串反序后进行base64解码，验证码就出来了。

![ibank1]({{site.img_url}}ibank8.png)

到这里我们已经可以非常容易的自动化识别验证码了。

下面再测试一下后台验证码验证的逻辑是否有效。先随便输入一个错误的验证码试试。

![ibank1]({{site.img_url}}ibank9.png)

服务器返回 Wrong code。

![ibank1]({{site.img_url}}ibank10.png)

这次输入任意的用户名密码和正确的验证码

![ibank1]({{site.img_url}}ibank11.png)

返回的错误信息是Identifier not found，根据之前的信息可以判断这里可以暴力破解。写一个脚本自动识别验证码就可以，不过这样子的效率太低了。先来测试一下验证码使用一次是否销毁不能再用了。

填写信息，使用burpsuite抓包。

![ibank1]({{site.img_url}}ibank12.png)

使用burpsuite的repeater发送多次这个请求，发现服务器返回 Identifier not found，即这个验证码可以使用多次。这样就可以忽略验证码直接暴力破解了。这种形式的验证码缺陷很常见，之前[乌云集市](http://wooyun.org/bugs/wooyun-2010-046547)就出现过这种。所以看到验证码不要慌乱，有些时候验证码有和没有一个样。

接下来就是利用burpsuite暴力破解账号，同时使用用户名和密码两个字典的详细步骤演示。对burpsuite熟悉的同学可以直接跳到文末，下载虚拟机自己玩去吧。


首先准备用户名和密码的字典，这里为了方便演示，用户名字典使用以下脚本生成。

#!/usr/bin/python

	f = open('Wordlist.txt', 'a')

	for num in range(1000000, 1000101):
	  f.write(str(num) + '\n')

	f.close()

密码使用 qwerty 和123456,1234567，当然用户名的范围以及弱密码实际上远不止这些，大家用自己的字典跑跑会有额外惊喜。

将登陆的请求发送到intruder，attack type选择cluster bomb，然后添加login和password两个变量。

![ibank1]({{site.img_url}}ibank13.png)

点击payloads标签，payload set 1,payload type 为Runtime file，Payload Options里选择用户名字典。同理payload set 2,也是一样的方式，选择密码字典。


![ibank1]({{site.img_url}}ibank14.png)

选择intruder标签的start，开始暴力破解。结束后按照Length排序，发现有3个请求的大小为3824,返回码是302.

![ibank1]({{site.img_url}}ibank15.png)

![ibank1]({{site.img_url}}ibank16.png)

使用账号1000001和密码1234567成功登陆系统。

![ibank1]({{site.img_url}}ibank17.png)

登陆系统之后，当然就是转账了。这个虚拟环境也设计了存在缺陷的交易验证码。感兴趣的同学可以自己测试一下。

