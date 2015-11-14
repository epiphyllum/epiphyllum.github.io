---
layout: post
title: "aliyun队列的一种封装"
description: ""
category: 
tags: []
---
{% include JB/setup %}

公司使用Aliyun的消息队列作为基础设施, 模仿REST, 我做了下封装， 基本思路如下:

1. 队列的消息是任意的Pojo对象, 对比于REST中的Representation
2. 定义服务接口, 类似于REST中的Resource, 定义了几个简单的annotation:  QueueName, MessagePram( path="/xxxx", delay=10 )
3. 服务端实现Resource接口
4. 提供客户端ResourceFactory, 客户端直接调用方法, 无需了解队列的存在!
5. 代码在我的github上[cloudservice-wrapper](https://github.com/epiphyllum/cloudservice-wrapper.git), 欢迎拍砖

一. 定义消息Pojo

{% highlight java %}

public class Contact {
    private String firstName;
    private String lastName;
    private int age;
    // ... getter setter and contructor
}

{% endhighlight %}

二.  定义服务接口

{% highlight java %}
@QueueName("helloQ")   // 采用helloQ作为消息队列传输数据
public interface ContactQResource {

   @MessageParam(path = "/getContact")
   void getContact(int id);

   @MessageParam(path = "/createContact", delay = 10)   //  延迟发送
   void createContact(Contact contact);
}
{% endhighlight %}

三. 服务实现
{% highlight java %}

public class ContactQResourceImpl implements ContactQResource {


    public ContactQResourceImpl() {
    }

    @Override
    public void getContact(int id) {
        System.out.println("server: get request " + id);
    }

    @Override
    public void createContact(Contact contact) {
        System.out.println("get createContact(" + contact +")");
    }
}
{% endhighlight %}

四. bootstrap服务端

{% highlight java %}
public class QServer {
    public static void main(String[] args) {
        QueueProvider queueProvider = new AliQueueProvider();
        QestServer qestServer = new QestServer(queueProvider);
        ContactQResource qResource = new ContactQResourceImpl();
        qestServer.run(qResource);
        System.out.println("questServer is started");
    }
}
{% endhighlight %}

五. bootstrap客户端
{% highlight java %}
public class QClient {
    public static void main(String[] args) {

        QueueProvider queueProvider = new AliQueueProvider();
        ContactQResource resource = QResourceFactory.getResource(ContactQResource.class, queueProvider);  // 取得Resource的本地代理对象
        // resource.getContact(10);

        resource.createContact(new Contact("hary", "zhou", 11));
        resource.createContact(new Contact("hary", "zhou", 22));
        resource.createContact(new Contact("hary", "zhou", 33));
    }
}
{% endhighlight %}

六. 代码在我的github上[cloudservice-wrapper](https://github.com/epiphyllum/cloudservice-wrapper.git), 欢迎讨论

