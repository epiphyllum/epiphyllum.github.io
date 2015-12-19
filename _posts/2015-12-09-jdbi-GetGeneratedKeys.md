---
layout: post
title: "jdbi @GetGeneratedKeys"
description: "jdbi @GetGeneratedKeys"
category: "java"
tags: ["java", "jdbi"]
---
{% include JB/setup %}

@GetGeneratedKeys在H2, MySQL下都没有问题，但是在SQLServer下需要变通一下才行。

假设有这么一张表(MySQL/H2)

{% highlight sql %}
drop table if exists contact;

create table contact(
    id int(11) not null auto_increment,
    firstName varchar(255) not null,
    lastName varchar(255) not null,
    phone varchar(30) not null,
    memo varchar(255),
    primary key(id)
)

{% endhighlight %}

JDBI的DAO可以这么写

{% highlight java %}
public interface InstallmentDAO {
    @GetGeneratedKeys
    @SqlUpdate("insert into contact(firstName, lastName, phone, memo) values(:firstName, :lastName, :phone, :memo)"
    String save(@BindBean Contact contact);
}
{% endhighlight %}

但是在SQLServer中需要这么写

{% highlight java %}
public interface InstallmentDAO {
    @GetGeneratedKeys
    @SqlQuery("insert into contact(firstName, lastName, phone, memo) values(:firstName, :lastName, :phone, :memo) OUTPUT INSERTED.Id"
    String save(@BindBean Contact contact);
}
{% endhighlight %}

