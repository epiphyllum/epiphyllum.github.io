---
layout: post
title: "junit4 and hamcrest test learn notes"
description: "junit and hamcrest test learn notes" 
category: "java"
tags: ["java", "test", "junit4", "junit", "hamcrest"]
---
{% include JB/setup %}

## Junit4用起来更加单
{% highlight groovy %}
import org.junit.Test;
import org.junit.Assert.*

@Test   // 标注测试方法
@Before // 每个测试方法执行之前的方法setUp
@After  // 每个测试方法执行之后的方法tearDown
@BeforeClass  // 测试开始之前运行
@AfterClass   // 测试结束之后运行

{% endhighlight %}

## org.junit.Assert.*下的断言不是很好用. 建议使用hamcrest

Hamcrest是一个书写匹配器对象时允许直接定义匹配规则的框架.

Hamcrest带有一个有用的匹配器库.以下是一些最重要的.
### 核心
{% highlight text %}
anything - 总是匹配,如果你不关心测试下的对象是什么是有用的
describedAs - 添加一个定制的失败表述装饰器
is - 改进可读性装饰器 - 见下 “Sugar”
{% endhighlight  %}

### 逻辑
{% highlight text %}
allOf - 如果所有匹配器都匹配才匹配, short circuits (很难懂的一个词,意译是短路,感觉不对,就没有翻译)(像 Java &&)
anyOf - 如果任何匹配器匹配就匹配, short circuits (像 Java ||)
not - 如果包装的匹配器不匹配器时匹配,反之亦然
{% endhighlight  %}

### 对象
{% highlight text %}
equalTo - 测试对象相等使用Object.equals方法
hasToString - 测试Object.toString方法
instanceOf, isCompatibleType - 测试类型
notNullValue, nullValue - 测试null
sameInstance - 测试对象实例
{% endhighlight  %}

### Beans
{% highlight text %}
hasProperty - 测试JavaBeans属性
{% endhighlight  %}

### 集合
{% highlight text %}
array - 测试一个数组元素test an array’s elements against an array of matchers
hasEntry, hasKey, hasValue - 测试一个Map包含一个实体,键或者值
hasItem, hasItems - 测试一个集合包含一个元素
hasItemInArray - 测试一个数组包含一个元素
{% endhighlight  %}

### 数字
{% highlight text %}
closeTo - 测试浮点值接近给定的值
greaterThan, greaterThanOrEqualTo, lessThan, lessThanOrEqualTo - 测试次序
{% endhighlight  %}

### 文本
{% highlight text %}
equalToIgnoringCase - 测试字符串相等忽略大小写
equalToIgnoringWhiteSpace - 测试字符串忽略空白
containsString, endsWith, startsWith - 测试字符串匹配
{% endhighlight  %}



