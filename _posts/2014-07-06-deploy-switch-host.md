---
layout: post
title: "利用ssh的proxy功能作生产部署跳板"
description: "生产部署跳板"
category: "tools"
tags: ['ssh', "forwardagent", "proxycommand" ]
---
{% include JB/setup %}


1. 情况描述
{% highlight text %}
生产环境有prod1 prod2 prod3三台生产机
机房跳板机为springboard
本地VPN访问主机为hwin
个人主机为mac
然后我这边希望, 从我mac上一次性登陆到prod1 prod2 prod3
{% endhighlight %}

2. 方法:

第一步:
在windows上安装rinetd程序， 设置端口转发22 -> springboard的 22

第二步:
在springboard, prod1, prod2, prod3上放置好本人的public key

第三步:
在mac的$HOME/.ssh/config中添加

{% highlight text %}
# 生产跳板
host deploy
    user hlwzf
    hostname hwin
    port 22
    identityfile ~/.ssh/id_rsa
    preferredauthentications publickey
    forwardagent yes    

#
# hostname填生产内网地址
# user 天生产主机的用户
#
host prod1
    hostname 10.100.12.251  
    port 22
    user appuser
    proxycommand ssh deploy -W %h:%p

host prod2
    hostname 10.100.12.252  
    port 22
    user appuser
    proxycommand ssh deploy -W %h:%p

host prod3
    hostname 10.100.12.253
    port 22
    user appuser
    proxycommand ssh deploy -W %h:%p

{% endhighlight %}

