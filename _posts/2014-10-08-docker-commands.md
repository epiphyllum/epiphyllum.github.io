---
layout: post
title: "docker commands notes"
description: "docker commands notes"
category: "tools"
tags: ["docker", "容器"]
---

{% include JB/setup %}


一. 查看容器的root用户密码
{% highlight bash %} 
docker logs <容器名或ID> 2>&1 | grep '^User: ' | tail -n1
{% endhighlight %} 

二. 查看容器日志
{% highlight bash %} 
docker logs -f <容器名或ID> 
{% endhighlight %} 

三. 查看正在运行的容器
{% highlight bash %} 
docker ps
docker ps -a  # 查看所有容器, 包括一家停止的
{% endhighlight %} 

四. 删除容器
{% highlight bash %} 
docker rm <容器名称或者ID>
docker rm $(docker ps -a -q)  # 删除所有容器
{% endhighlight %} 

五. 停止启动杀死容器
{% highlight bash %} 
docker stop <容器名称或ID>
docker start <容器名称或ID>
docker kill <容器名称或ID>
{% endhighlight %} 

六. 查看镜像, 删除镜像
{% highlight bash %} 
docker images
docker rmi 镜像名称
{% endhighlight %} 

七. 创建并运行容器
{% highlight bash %} 
docker run --name redmine        # 给容器名称
           -p 9003:80            # 将9003映射到容器的80
           -p 9023:22            # 将9023映射到容器的22
           -d                    # daemon
           -v /var/redmine/files:/redmine/files    # 挂载/var/redmine/files到容器的/redmine/files
           -v /var/redmine/mysql:/var/lib/mysql 
           sameersbn/redmine     # 镜像名称
{% endhighlight %} 

八. 当需要把一台机器上的镜像迁移到另一台机器的时候，需要保存镜像与加载镜像。
{% highlight bash %} 
docker save busybox-1 > /home/save.tar
{% endhighlight %} 
使用scp将save.tar拷到机器b上，然后：

{% highlight bash %} 
docker load < /home/save.tar
{% endhighlight %} 

九. 重新查看container的stdout(docker attach)
{% highlight bash %} 
ID=$(sudo docker run -d ubuntu /usr/bin/top -b)
docker attach $ID
{% endhighlight %} 

十. 从container中拷贝文件出来
{% highlight bash %} 
docker cp 7bb0e258aefe:/etc/debian_version .
{% endhighlight %} 

十一. 在容器中执行其他程序(docker exec)
{% highlight bash %} 
docker exec -it <容器名称或id>  bash
{% endhighlight %} 

