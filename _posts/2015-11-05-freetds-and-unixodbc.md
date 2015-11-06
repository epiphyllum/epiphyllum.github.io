---
layout: post
title: "unixodbc and freetds"
description: "connect to SqlServer under linux with freetds"
category: "tools"
tags: [ "unixodbc", "freetds" ]
---

## ubuntu 14.04

一. 安装unixodbc, freetds

{% highlight bash %}
sudo apt-get install freetds-bin freetds-common freetds-dev libct4 libsybdb5 \  
    unixodbc unixodbc-dev unixodbc-bin libodbc1 odbcinst1debian2
{% endhighlight %}


## mac

一. 安装

{% highlight bash %}
brew install unixodbc  
brew install freetds --with-unixodbc
{% endhighlight %}

二. 准备配置文件(/usr/local/etc/freetds.conf)

{% highlight properties %}
[cactus]  
host = cactus  
port = 1433  
tds version = 8.0  
{% endhighlight %}

三. 准备配置文件(/usr/local/etc/odbcinst.ini)

{% highlight properties %}
[FreeTDS]
Description = FreeTDS Driver v0.91  
Driver = /usr/local/Cellar/freetds/0.91_2/lib/libtdsodbc.so  
Setup = /usr/local/Cellar/freetds/0.91_2/lib/libtdsodbc.so  
Trace = yes  
TraceFile = /tmp/odbc.log  
fileusage=1  
dontdlclose=1  
UsageCount=1  

{% endhighlight %}

四. 准备配置文件(/usr/local/etc/odbc.ini)

{% highlight properties %}
[cactus]  
Description = MSSQL Server  
Driver = FreeTDS  
Server = cactus  
Port  = 1433  
tds_version = 8.0  
{% endhighlight %}

五. Perl测试程序

{% highlight perl %}
#!/usr/bin/perl
use warnings;
use strict;
use DBI;
use Data::Dump;

my $dbh = DBI->connect(
    'dbi:ODBC:cactus',
    'hary',
    'jessie',
    {

      RaiseError => 1,
      AutoCommit => 0
    }
) || die "Database connection not made: $DBI::errstr";

warn "连接数据库成功";

$dbh->do("use batchtest");

warn "设置schema成功";

my $sth = $dbh->prepare(qq/select top 10 * from ApplicationOverDueObjects1103/);

$sth->execute();

while( my $href = $sth->fetchrow_hashref ) {
    Data::Dump->dump($href);
}

{% endhighlight %}


