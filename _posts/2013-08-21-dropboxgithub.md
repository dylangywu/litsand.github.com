---
layout: post
title: "用dropbox做git私人仓库"
description: ""
category: 工具使用
tags: [dropbox,git]
---
{% include JB/setup %}

在dropbox同步的文件夹内执行

	git init --bare mytestrepo.git

然后在本地创建git工作目录

	$ cd ~
	$ mkdir testrepo
	$ cd testrepo
	$ git init

添加远程仓库:

	git remote add origin ~/Dropbox/Git/mytestrepo.git


然后  git add,commit,push就可以了.

	git add . && git commit -m "auto" && git push


## 参考资料 ##

[Using Dropbox as a Private GitHub](http://jetheis.com/blog/2013/02/17/using-dropbox-as-a-private-github/http://jetheis.com/blog/2013/02/17/using-dropbox-as-a-private-github/)

[Using Git and Dropbox together effectively?](http://stackoverflow.com/questions/1960799/using-git-and-dropbox-together-effectively)

