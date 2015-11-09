---
layout: post
title: "spock test notes"
description: "spock test笔记"
category: "java"
tags: ["java", "groovy", "spock", "test"]
---
{% include JB/setup %}

### imports
{% highlight groovy %}
import spock.lang.*
{% endhighlight %}

### Specification
{% highlight groovy %}
class MySpecification extends Specification {
    // fields
    def obj = new ClassUnderSpecification()
    def col1 = new Collaborator()

    @Shared res = new VeryExpensiveResource()

    // constants
    static final PI = 3.141592654

    // fixture methods
    def setup() {}            // 每个测试方法之前执行
    def cleanup() {}          // 每个测试方法之后执行
    def setupSpec() {}        // 第一个测试方法之前执行
    def cleanupSpec() {}      // 最后一个测试方法之后执行

    // feature methods
    def "测试方法-specification"() {
        given:  "是setup的别名"
        def file = new File("/some/path")
        def stack = new Stack()
        def elem = "push me"

        when:  "Stimulus"

        then:  "Response"
        stack.size() == 1
        stack.peek() == elem
        !stack.empty
        thrown(EmptyStackException)
        EmptyStackException e = thrown()
        notThrown(EmptyStackException)

        1 * subscriber1.recieve("event")   // 交互测试
        1 * subscriber2.recieve("event")   // 交互测试

        expect:  "Stimulus + Response"
        Math.max(1,2) == 2

        cleanup:  "Cleanup"
        file.delete()

        where:  "Setup + Stimulus + Response + Cleanup"
    }

    // help methods
}
{% endhighlight %}

### Data Driven Testing
适合于纯函数测试
{% highlight groovy %}
class MathSpec extends Specification {

    // 这个annotation将给出所有的报告
    @Unroll
    def "maximum of two numbers"() {
        expect: 
        Math.max(a, b) == c

        where:
        a  |  b  || c
        1  |  2  || 2
        2  |  1  || 2
        1  |  1  || 1
  
        // 也可以用Data Pipe语法 :  Collection, String, Iterable
        // where:
        // a << [ 1,2,1 ]
        // b << [ 2,1,1 ]
        // c << [ 2,2,2 ]
    }
   
}
{% endhighlight %}

多变量的DataPipe
{% highlight groovy %}
@Shared sql = Sql.newInstance("jdbc:h2:mem:", "org.h2.Driver"")

def "maximum of two numbers"() {
    where:
    [a, b, c] << sql.rows("select a, b, c from maxdata")
    [x, y, _, z]  << sql.rows("select * from datarow")     // ignored with _
}
{% endhighlight %}


DataTable, DataPipe, Assignment可以共用
{% highlight groovy %}

...
where:
a | _       // data table
3 | _
7 | _
0 | _

b << [5, 0, 0]       // data pipe

c = a > b ? a : b    // assignment 

{% endhighlight %}


### 交互测试

mock
{% highlight groovy %}

def subscriber1 = Mock()   // 
def subscriber2 = Mock()   // 

when:
publisher.send("hello")

then:
1 * subscriber1.recieve("hello")
1 * subscriber2.recieve("hello")
(1..3) * subscriber2.recieve("hello")
(1.._) * subscriber2.recieve("hello")
(_..3) * subscriber2.recieve("hello")

// 方法constraints
(_..3) * subscriber2./r.*e/("hello")    

// 参数constraints
(_..3) * subscriber2.recieve(_ as String)    
(_..3) * subscriber2.recieve({ it.size() > 3 })
(_..3) * subscriber2.recieve(!null)
(_..3) * subscriber2.recieve(_)
(_..3) * subscriber2.recieve(*_)
(_..3) * subscriber2.recieve(!"hello")

1 * process.invoke("ls", "-a", _, !null, { ["abc", "def"].contains(it)} )

interaction {
    def message = "hello"
    1 * subscriber1.receive(message)
}

// 测试invocation order
// 假设发送消息为: "hello" "hello" "goodbye", "hello" "goodbye" "hello"
then:
2 * subscriber.recieve("hello")
1 * subscriber.recieve("goodbye")
1 * subscriber.recieve("hello")
1 * subscriber.recieve("goodbye")
1 * subscriber.recieve("hello")

{% endhighlight %}

stub
{% highlight groovy %}



given:
def subscriber = Stub(Subscriber)
Subscriber subscriber = Stub()

subscriber.recieve >> "abc"
subscriber.recieve >>> []
subscriber.recieve >> { args -> args[0].size() > 3 "ok" : "fail" }
subscriber.recieve >> { throw new InternalError("ouch") }
subscriber.recieve >>> ["ok", "fail", "ok"] >> { throw new InternalError() } >> "ok"   // chained

//
def subscriber = Stub(Subscriber) {
    recieve("message1") >> "ok"
    recieve("message2") >> "fail"
}

{% endhighlight %}

结合mock与stub

{% highlight groovy %}
then:
1 * subscriber.recieve("message1") >> "ok"        // "message1"消息收到一次且处理结果是ok
1 * subscriber.recieve("message2") >> "fail"
{% endhighlight %}


Spyies 用于对具体对象的部分功能覆盖。 比方说A类有x, y, z三个方法，我们想改变z方法的，但x, y方法保持不变
{% highlight groovy %}

given:
def subscriber = Spy(SubscriberImpl, constructorArgs: ["Fred"])
subscriber.recieve(_) >> { String message -> callRealMethod(); message.size() > 3 ? "ok":"fail" }

{% endhighlight %}


