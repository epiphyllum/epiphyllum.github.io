---
layout: post
title: "Spring4范型service注入"
description: "Spring4范型service注入"
category: "java"
tags: ["java", "spring", "generic"]
---
{% include JB/setup %}

实体
{% highlight java %}
public class User implements Serializable {  
    private Long id;  
    private String name;  
}  
  
public class Organization implements Serializable {  
    private Long id;  
    private String name;  
}  
{% endhighlight %}

数据访问Repository
{% highlight java %}
public abstract class BaseRepository<M extends Serializable> {  
    public void save(M m) {  
        System.out.println("=====repository save:" + m);  
    }  
}  
  
@Repository  
public class UserRepository extends BaseRepository<User> {  
}  
  
@Repository  
public class OrganizationRepository extends BaseRepository<Organization> {  
}  
{% endhighlight %}

之前的注入的服务: 每个必须写个setter
{% highlight java %}

public abstract class BaseService<M extends Serializable> {  
    private BaseRepository<M> repository;  
    public void setRepository(BaseRepository<M> repository) {  
        this.repository = repository;  
    }  
    public void save(M m) {  
        repository.save(m);  
    }  
}  

@Service  
public class UserService extends BaseService<User> {  
    @Autowired  
    public void setUserRepository(UserRepository userRepository) {  
        setRepository(userRepository);  
    }  
}  
  
@Service  
public class OrganizationService extends BaseService<Organization> {  
    @Autowired  
    public void setOrganizationRepository(OrganizationRepository organizationRepository) {  
        setRepository(organizationRepository);  
    }  
}  

{% endhighlight %}

## 范型注入的服务
{% highlight java %}

// 抽象服务类
public abstract class BaseService<M extends Serializable> {  

    // 这里发生了范型注入！！！！！！！
    @Autowired  
    protected BaseRepository<M> repository;  
  
    public void save(M m) {  
        repository.save(m);  
    }  
}  

// 具体的用户服务类  
@Service  
public class UserService extends BaseService<User> {  
}  
  
// 具体的组织服务类
@Service  
public class OrganizationService extends BaseService<Organization> {  
}

{% endhighlight %}
