---
layout: post
title: "proc_root undeclared"
description: ""
category: 代码人生
tags: [kernel]
---
{% include JB/setup %}

参考http://stackoverflow.com/questions/2531730/linux-kernel-module-creating-proc-file-proc-root-undeclared-error

That example is out of date. Under the current kernel API, pass NULL for the root of procfs.

Also, instead of create_proc_entry, you should use proc_create() with a proper const struct file_operations *.
