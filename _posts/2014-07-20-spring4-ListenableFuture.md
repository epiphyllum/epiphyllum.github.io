---
layout: post
title: "Spring4 ListenableFuture"
description: "Spring4 ListenableFuture"
category: "java"
tags: ["java", "addShutdownHook"]
---
{% include JB/setup %}


引入类
{% highlight java %}
import org.springframework.util.concurrent.ListenableFutureCallback;
import org.springframework.util.concurrent.ListenableFutureTask;

import java.util.concurrent.Callable;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
{% endhighlight %}

{% highlight java %}

ListenableFutureTask<String> task = new ListenableFutureTask<>( new Callable() {
    @Override
    public String call() throws Exception {
        Thread.sleep(1000);
        return "hello world";
    }
});

task.addCallback(new ListenableFutureCallback<String>() {
    @Override
    public void onSuccess(String result){}

    @Override
    public void onFailure(Throwable e){}
});

executorService.submit(task);
String result = task.get();   // 阻塞

{% endhighlight %}

