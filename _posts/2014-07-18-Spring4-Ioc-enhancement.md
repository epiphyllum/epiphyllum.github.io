---
layout: post
title: "Spring4 Ioc Enhancement"
description: "Spring4 Ioc Enhancement"
category: "java"
tags: ["java", "spring"]
---
{% include JB/setup %}

一、Map依赖注入
{% highlight java %}

// 这里将作如下注入:  key是bean的名称, value是所有实现了BaseService的Bean
@Autowired
private Map<String, BaseService>  map;A

{% endhighlight %}

二、List/数组注入
{% highlight java %}

// 这里会将所有实现了BaseService的Bean注入到list中， 单这里是没有顺序的
@Autowired
private List<BaseService> list;

// 如果需要顺序, 那么可用@Order来注解服务
@Order(1)
@Service
public class UserService extends BaseService<User>{}
{% endhighlight %}


三、@Lazy可以延迟依赖注入
{% highlight java %}
@Lazy
@Service
public class UserService extends BaseService<User>{}

@Lazy
@Autowired
private UserService userService;
{% endhighlight %}

四、@Conditional
{% highlight java %}
@Profile("local")
@Profile("remote")
@ActiveProfiles("remote")
@Conditional()
{% endhighlight %}
[参考](http://jinnianshilongnian.iteye.com/blog/1989379);

五、Spring内联了objenesis类库, 这样基于CGLIB的代理类不在要求必须有空参构造函数了

