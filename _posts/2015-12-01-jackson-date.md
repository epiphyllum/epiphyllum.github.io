---
layout: post
title: "jackson date"
description: "jackson and date"
category: "java"
tags: ["java", "jackson", "json", "date"]
---
{% include JB/setup %}

import com.fasterxml.jackson.databind.ObjectMapper;
ObjectMapper默认Date字段会被序列化为unix epoch time, 如果需要将期序列化为制定格式
需要作如下配置
{% highlight groovy %}
objectMapper.setDateFormat(new SimpleDateFormat("YYYY-mm-dd HH:MM:SS"));
{% endhighlight %}

或者使用@JsonFormat来注解字段
{% highlight groovy %}
{% endhighlight %}

