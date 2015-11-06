---
layout: post
title: "unixodbc and freetds"
description: "connect to SqlServer under linux with freetds"
category: "tools"
tags: [ "unixodbc", "freetds" ]
---

## ubuntu 14.04

1. 安装unixodbc, freetds

    apt-get install freetds-bin freetds-common freetds-dev libct4 libsybdb5 \  
                    unixodbc unixodbc-dev unixodbc-bin libodbc1 odbcinst1debian2


## mac

1. 安装

    brew install unixodbc  
    brew install freetds --with-unixodbc

2. 准备配置文件(freetds.conf)

```
[cactus]  
    host = cactus  
    port = 1433  
    tds version = 8.0  
```

3. 准备配置文件(odbcinst.ini)

```
[FreeTDS]
Description = FreeTDS Driver v0.91  
Driver = /usr/local/Cellar/freetds/0.91_2/lib/libtdsodbc.so  
Setup = /usr/local/Cellar/freetds/0.91_2/lib/libtdsodbc.so  
Trace = yes  
TraceFile = /tmp/odbc.log  
fileusage=1  
dontdlclose=1  
UsageCount=1  
```

4. 准备配置文件(odbc.ini)

```
[cactus]  
Description = MSSQL Server  
Driver = FreeTDS  
Server = cactus  
Port  = 1433  
tds_version = 8.0  
```


{% include JB/setup %}
