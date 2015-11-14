---
layout: post
title: "aliyun队列的一种封装"
description: ""
category: 
tags: []
---
{% include JB/setup %}

1. 定义消息Pojo

{% highlight java %}

public class Contact {
    private String firstName;
    private String lastName;
    private int age;
    // ... getter setter and contructor
}

{% endhighlight %}

2.  定义服务接口

{% highlight java %}
@QueueName("helloQ")    
public interface ContactQResource {

   @MessageParam(path = "/getContact")
   void getContact(int id);

   @MessageParam(path = "/createContact")
   void createContact(Contact contact);
}
{% endhighlight %}

3. 服务实现
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

4. bootstrap服务端

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

5. bootstrap客户端
{% highlight java %}
public class QClient {
    public static void main(String[] args) {
        QueueProvider queueProvider = new AliQueueProvider();
        ContactQResource resource = QResourceFactory.getResource(ContactQResource.class, queueProvider);
        // resource.getContact(10);

        resource.createContact(new Contact("hary", "zhou", 11));
        resource.createContact(new Contact("hary", "zhou", 22));
        resource.createContact(new Contact("hary", "zhou", 33));
    }
}
{% endhighlight %}

