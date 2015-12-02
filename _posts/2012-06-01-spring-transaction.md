---
layout: post
title: "Spring transaction propagation"
description: "Spring中事务的传播熟悉"
category: "java"
tags: ["spring", "transaction", "propagation"]
---
{% include JB/setup %}

一. @Transactional 默认的propgationi事REQUIRED(没有就创建事物, 有就在当前事物中执行)


1. read uncommitted

2. read committed

3. repeatabe read

4. serialization


--

required

required_new    没有就创建, 有就将原事物挂起
mandatory       只能在一个已有事务中进行, 当前没有事务就抛出异常
supports        有、没有都可以
not_supported   
netsted -  只对DatasourceTransactionManager有效！！！  
nerver  -  不能在事物上下文中进行





