---
layout: post
title: "Dockerfile notes"
description: "Dockerfile notes"
category: "tools"
tags: ["docker", "Dockerfile", "容器"]
---

{% include JB/setup %}


# 构建镜像

{% highlight text %} 
docker build -t hary/ubuntu-java8:1.0  .
{% endhighlight %} 

# 常用几个Dockefile中的命令

{% highlight bash %} 
FROM ubuntu            # 从哪里继承  ubuntu:14.04  hary/ubuntu:14.04
MAINTAINER hary 
ENV  DT_HOME  /app/dt  # 设置环境变量, 程序中可以使用ENV设置的环境变量
ADD  file.tar /app     # 会建立/app/file目录, file.tar是相对于Dockerfile所在目录的
VOLUME  /app/dt/log    # 可以将本地目录挂载到/app/dt/log下。 在run的时候使用参数 -v local-path:/app/dt/log
WORKDIR /app/dt        # 切换目录， 可以在Dockerfile中多次切换目录!, 对RUN, CMD, ENTRYPOINT有效
EXPOSE 8080            # 开启内部服务端口, 启动时可用参数:  -p 127.0.0.1:33301:8080 将外部33301映射到8080
USER                   # 使用哪个用户跑container
{% endhighlight %} 

# 区别CMD ENTRYPOINT

## CMD

{% highlight text %} 
1> CMD ["executable","param1","param2"] (exec form, this is the preferred form)
2> CMD ["param1","param2"]  提供默认参数给ENTRYPOINT
3> CMD command param1 param2 和1一样
{% endhighlight %} 

CMD的主要作用是为容器提供默认的执行命令, 或者给Entry Point提供默认的参数

## ENTRYPOINT

{% highlight text %} 
ENTRYPOINT ["executable", "param1", "param2"] (exec form, the preferred form)
ENTRYPOINT command param1 param2 (shell form)
{% endhighlight %} 

Dockerfile中只能有一个ENTRYPOINT
ENTRYPOINT主要是用来提供容器执行一直部分, ENTRYPOINT可以被继承。 

## 举例如下

Dockerfile A 构建出的镜像是 hary/a

{% highlight text %} 
FROM ubuntu:14.04
CMD ["-l"]
ENTRYPOINT ["ls"]
{% endhighlight %} 

Dockerfile B 从hary/a继承, 生成的镜像是hary/b

{% highlight text %} 
FROM hary/a
CMD ["-l", "/var"]
{% endhighlight %} 

执行如下:

{% highlight text %} 
docker run -it  --rm hary/a        # 实际执行的就是 ls -l
docker run -it  --rm hary/a  /usr  # 实际执行的就是 ls /usr
docker run -it  --rm hary/b        # 实际执行的就是 ls -l /var
docker run -it  --rm hary/b -a /root/  # 实际执行的就是 ls -a /root
{% endhighlight %} 

可以看到:
如果容器启动时带参数， 则覆盖CMD的的参数。
如果容器启动时不带参数，则用CMD的参数作为ENTRYPONIT的参数
如果容器没有ENTRYPOINT, 则默认用CMD的启动
如果容器没有ENTRYPOINT, 可以在启动时重新提供启动命令













