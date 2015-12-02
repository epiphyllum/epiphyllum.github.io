---
layout: post
title: "Spring4 ResolvableType总结"
description: "Spring4 ResolvableType notes"
category: "java"
tags: ["java", "spring", "ResolvableType"]
---
{% include JB/setup %}

引入对象
{% highlight java %}
import org.springframework.util.ClassUtils;
import org.springframework.util.ReflectionUtils;
import org.springframework.core.ResolvableType;
{% endhighlight %}

测试类
{% highlight java %}
class A {}
class B {}
class C {}
class D {}
interface Service<N, M>{}
class ABService implements Service<A, B> {}
class CDService implements Service<C, D> {}
{% endhighlight %}

测试
{% highlight java %}
private Service<A, B> abService;
private Service<C, D> cdService;
private List<List<String>> list;
private Map<String, Map<String, Integer>> map;
private List<String>[] array;

private HashMap<String, List<String>> method()  { return null; }
static class Const {
  public Const(List<List<String>> list, Map<String, Map<String, Integer>> map) {}
}

//获取类的ResovlableType, 如果ABService被代理, 需要用ClassUtils.getUserClass(ABService.class)
ResolvableType rt = ResolvableType.forClass(ABService.class);

rt.getInterfaces()[0].getGeneric(1).resolve();   // 得到Class<B>
rt.as(Service.class).getGeneric(1).resolve();    // 得到Class<B>

// 得到字段
ResolvableType rt2 = ResolvableType.forField(ReflectionUtils.findField(this.class, "cdService"));
rt2.getGeneric(0).resolve(); //  Class<C>

// 嵌套 List<String>[] array;
ResolvableType rt3 = ResolvableType.forField(ReflectionUtils.findField(this.getClass(), "list"));
rt3.getGeneric(0).getGeneric(0).resolve();  // String.class

// map嵌套  Map<String, Map<String, Integer>> map;
ResolvableType rt4 = ResolvableType.forField(ReflectionUtils.findField(this.getClass(), "map"));
rt4.getGeneric(1).getGeneric(1).resolve();  // Map<String, Integer> => Integer

// 方法返回值HashMap<String, List<String>>
ResolvableType rt5 = ResolvableType.forMethodReturnType(ReflectionUtils.findMethod(this.getClass(), "method");
rt5.getGeneric(1,0).resolve(); //String

// 构造参数Const(List<List<String>> list, Map<String, Map<String, Integer>> map)
ResolvableType rt6 = ResolvableType.forConstructorParameter(ClassUtils.getConstructorIfAvailable(Const.class, List.Class, Map.class), 1);  // 得到第一个参数map
rt6.getGeneric(1,0);  // String.class

// 数组
ResolvableType rt7 = ResolvableType.forField(ReflectionUtils.findField(this.getClass(), "array"));
rt7.isArray();
rt7.getComponentType().getGeneric(0).resolve();

//自定义泛型数组 List<String>[]
ResolvableType rt8 = ResovalbleType.forClassWithGenerics(List.class, String.class);  // source = List, generices = <String, ...>
ResolvableType rt9 = ResovalbleType.forArrayComponent(rt8);
rt9.getComponentType.getGeneric(0) // List<String> -->  String

// 比较两范型是否可以赋值成功
rt7.isAssignableFrom(rt9); // true

{% endhighlight %}

