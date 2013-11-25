---
layout: post
title: "利用css伪类暴力破解csrf token"
description: ""
category: 技术研究
tags: [css]
---
{% include JB/setup %}

《web前段黑客技术揭秘》的2.6.3CSS伪类中提到可以利用：visited伪类进行css history攻击。后面有个提问，这个有何危害。当时觉得可以获得用户的浏览记录，也许可以暴力猜测用户的一些内网地址，常用的网站。

今天看到一个利用css history 破解csrf token。而且有人写出了纯css代码实现。

http://archive.is/mT45

不过书上提到这个方式已经被浏览器修补了。

可见当时的思考是不全面的。这个问题的危害不仅仅是泄露一个地址，确切说是泄露一个url。只要存在url中的信息就有可能被骇客猜到。就是不安全的。所以csrf token放在url里也存在这种风险。
