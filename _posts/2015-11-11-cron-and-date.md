---
layout: post
title: "当cron遇到date命令"
description: "cron中的date命令"
category: "bash"
tags: ["bash cron date"]
---
{% include JB/setup %}

今天配置crontab遇到一个很奇怪的问题， 配置如下:
40 * * * * /home/hary/bin/backup.pl `/bin/date +%Y%m%d`
本意是想backup程序执行时, 每次都带上当前的日期作为参数
单这个是错误的。 


正确的做法是
{% highlight bash %}
40 * * * * /home/hary/bin/backup.pl `/bin/date +\%Y\%m\%d`
{% endhight %}
