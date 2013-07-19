---
layout: post
title: "server status 信息引起的信息泄漏"
description: ""
category: 
tags: []
---
{% include JB/setup %}

http://www.apache.org/server-status
server-status没有限制访问，导致信息泄漏。
<img src="/upload/201307/19095948b272b9d9f0164d9e8b7d981b19d1664b.png" alt="apache1.png" />
<img src="/upload/201307/1910014158a28a5fa2ab4f6c00b438c081729f3d.png" alt="apache2.png" />
<img src="/upload/201307/19100154671cb2dd81492e3eef74f15fcee467d0.png" alt="apache3.png" />
泄漏的信息点：
1,Apache Server Status for www.apache.org (via 140.211.11.131) 这个说明泄露了真实ip，不过经过测试apache也没有做cdn，应该有做负载均衡，有时候刷新，这个说明的ip会变成另外一个，经测试直接访问ipapache的主页。
2,Server uptime: 3 days 4 hours 48 minutes 43 seconds
Server load: 8.24 8.77 9.57
Total accesses: 35420283 - Total Traffic: 1706.5 GB
CPU Usage: u4146.86 s1566 cu0 cs0 - 2.07% CPU load
128 requests/sec - 6.3 MB/second - 50.5 kB/request
88 requests currently being processed, 552 idle workers 
性能信息，看起来才跑了3天，经常挂么难道。流量1700G，很是惊人。。。
3,58.179.141.82	wiki-online.apache.org:80	GET /wiki/modernized/img/moin-www.png 
client 泄露了客户端的ip，在有一些场景下，客户端ip也是隐私，不应该泄漏。
4,vhost 泄露了apache上跑得多个网站。这个对渗透的信息收集也是很有用处的。比如各种旁站啥的。可以看到wiki-online.apache.org:80 www.apache.org:80 archive.apache.org:80 httpd.apache.org:80 www.openoffice.org:80  。都是跑在这上面的。原来openofffice也在这里。
5,泄露了url。某些禁止搜索引擎收录的url都会真实的现实出来。比如，是不是可以通过对url的长期分析来找管理后台。
本文纯属YY，不当之处还请指正。
