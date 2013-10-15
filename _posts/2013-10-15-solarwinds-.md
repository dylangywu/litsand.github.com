---
layout: post
title: "solarwinds 报表分析"
description: ""
category: 
tags: [report]
---
{% include JB/setup %}

solarwinds的报表是利用一个专门的report客户端连接数据库,利用rpt模板产生报表.做的比较简陋相对来说.

报表分为3大类:audit,security,support,一共246个报表.

## audit子类: ##

1. authentication:(域各种权限的审计)
2. file audit
3. resource configure
4. networktrafic (目前不具备)

## security ##

1. event summary
2. authentication
3. malicious code
4. suspicious behaviour
5. machine audit
6. attack behavior-access
7. attack behavior-denial-relay

## support ##
自身的一些数据统计

1. internal system
2. incident
3. database maintenance

