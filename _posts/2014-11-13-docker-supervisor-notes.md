---
layout: post
title: "docker中的多服务的启动"
description: "docker中的多服务启动"
category: "tools"
tags: ["docker", "supervisor"]
---

{% include JB/setup %}

supervisor是一个服务管理工具

{% highlight bash %} 
FROM ubuntu:14.04

MAINTAINER epiphyllum 94093146@qq.com

RUN apt-get update
RUN apt-get install -y supervisor openssh-server
RUN mkdir /var/run/sshd

ADD supervisord.conf /etc/supervisord.conf

EXPOSE 22 80

CMD ["/usr/bin/supervisord"]

{% endhighlight %} 

