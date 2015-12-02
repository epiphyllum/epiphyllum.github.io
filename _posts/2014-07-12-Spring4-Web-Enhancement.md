---
layout: post
title: "Spring4 Web Enhancement"
description: "Spring4 Web Enhancement"
category: "java"
tags: ["java", "spring"]
---
{% include JB/setup %}

从Spring4开始，Spring以Servlet3为进行开发，如果用Spring MVC,
测试框架的话需要指定Servlet3兼容的jar包（因为其Mock的对象都是基于Servlet3的）.
另外为了方便Rest开发，通过新的@RestController指定在控制器上，这样就不需要在每个@RequestMapping方法上加 @ResponseBody了。
而且添加了一个AsyncRestTemplate ，支持REST客户端的异步无阻塞支持。

一、@RestController
{% highlight java %}
@RestController  
public class UserController {  
    private UserService userService;  
    @Autowired  
    public UserController(UserService userService) {  
        this.userService = userService;  
    }  
    @RequestMapping("/test")  
      public User view() {  
        User user = new User();  
        user.setId(1L);  
        user.setName("haha");  
        return user;  
    }  
  
    @RequestMapping("/test2")  
    public String view2() {  
        return "{\"id\" : 1}";  
    }  
}  
{% endhighlight %}

二、mvc:annotation-driven配置变化
统一风格；将enableMatrixVariables改为enable-matrix-variables属性；
将ignoreDefaultModelOnRedirect改为ignore-default-model-on-redirect。

三、提供AsyncRestTemplate用于客户端非阻塞异步支持。

服务端:
{% highlight java %}
@RestController  
public class UserController {  

    private UserService userService;  

    @Autowired  
    public UserController(UserService userService) {  
        this.userService = userService;  
    }  

    // 返回Callable
    @RequestMapping("/api")  
    public Callable<User> api() {  
        System.out.println("=====hello");  
        return new Callable<User>() {  
            @Override  
            public User call() throws Exception {  
                Thread.sleep(10L * 1000); //暂停两秒  
                User user = new User();  
                user.setId(1L);  
                user.setName("haha");  
                return user;  
            }  
        };  
    }  
}  
{% endhighlight %}

客户端:
{% highlight java %}
public static void main(String[] args) {  

    AsyncRestTemplate template = new AsyncRestTemplate();  

    //调用完后立即返回（没有阻塞）  
    ListenableFuture<ResponseEntity<User>> future = template.getForEntity("http://localhost:9080/spring4/api", User.class);  

    //设置异步回调  
    future.addCallback(new ListenableFutureCallback<ResponseEntity<User>>() {  
        @Override  
        public void onSuccess(ResponseEntity<User> result) {  
            System.out.println("======client get result : " + result.getBody());  
        }  
  
        @Override  
        public void onFailure(Throwable t) {  
            System.out.println("======client failure : " + t);  
        }  
    });  
    System.out.println("==no wait");  
} 
{% endhighlight %}

四、MvcUriComponentsBuilder: 从控制器获取URI信息

真实环境中, 存在两个@RequestMapping("/{id}")是错误的。当前只是为了测试。
{% highlight java %}
@Controller  
@RequestMapping("/user")  
public class UserController {  
  
    @RequestMapping("/{id}")  
    public String view(@PathVariable("id") Long id) {  
        return "view";  
    }  
  
    @RequestMapping("/{id}")  
    public A getUser(@PathVariable("id") Long id) {  
        return new A();  
    }  
  
}  
{% endhighlight %}


{% highlight java %}

import static org.springframework.web.servlet.mvc.method.annotation.MvcUriComponentsBuilder.*;  

// fromController(..).build().toString()
// fromMethodName(..).build().toString()
// fromMethodCall(on(UserController.class)).getUser(2L).build().toString()

@Test  
public void test() {  
    MockHttpServletRequest req = new MockHttpServletRequest();  
    RequestContextHolder.setRequestAttributes(new ServletRequestAttributes(req));  
  
    //MvcUriComponentsBuilder类似于ServletUriComponentsBuilder，但是直接从控制器获取  
    //类级别的  
    System.out.println(  
        fromController(UserController.class).build().toString()    
    );  
  
    //方法级别的  
    System.out.println(  
            fromMethodName(UserController.class, "view", 1L).build().toString()  
    );  
  
    //通过Mock方法调用得到  
    System.out.println(  
            fromMethodCall(on(UserController.class).getUser(2L)).build()  
    );  
}  

{% endhighlight %}

注意：当前MvcUriComponentsBuilder实现有问题，只有JDK环境支持，大家可以复制一份，然后修改：
method.getParameterCount() （Java 8才支持） 到method.getParameterTypes().length



