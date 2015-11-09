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
    // fields,  每个feature方法都会重新创建
    def obj = new ClassUnderSpecification()
    def col1 = new Collaborator()

    // 只被创建一次
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

        // then部分， 每个表达式都是个boolean!!!!
        then:  "Response"
        stack.size() == 1
        stack.peek() == elem
        !stack.empty
        thrown(EmptyStackException)      // 抛出了指定类型的异常
        EmptyStackException e = thrown() // 获得抛出的异常!!!
        notThrown(EmptyStackException)   // 没有抛出异常

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
@Shared sql = Sql.newInstance("jdbc:h2:mem:", "org.h2.Driver")

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

mock: 描述被测试对象(object under specification)与协作对象(collaborators)之间的交互!!
{% highlight groovy %}

def subscriber = Mock(Subscriber)   // 

def Subscriber subscriber1 = Mock()   //  推荐!!!
def Subscriber subscriber2 = Mock()   // 


def  "should send messages to all subscribers"() {
  when:
  publisher.send("hello")

  then:  
  1 * subscriber1.recieve("hello")       // subscriber1.recieve("hello") 被调用一次
  1 * subscriber2.recieve("hello")
}
// 这里可以这么解读: 
// 当publisher发布一个hello消息, 
// 则subsriber1与subscriber2都会收到hello消息一次


// 基数限制: Cardinality constraint
(1..3) * subscriber2.recieve("hello")  // 1到三次
(1.._) * subscriber2.recieve("hello")  // 1次以上
(_..3) * subscriber2.recieve("hello")  // 0到三次

// 方法constraints
(_..3) * subscriber2./r.*e/("hello")   // 方法满足正则

// 目标constraints:
1 * /^sub*/.recieve("hello")

// 参数constraints
(_..3) * subscriber2.recieve(_ as String)        // 参数为字符串
(_..3) * subscriber2.recieve({ it.size() > 3 })  // 参数的size > 3
(_..3) * subscriber2.recieve(!null)              // 参数不为null
(_..3) * subscriber2.recieve(_)                  // 任何参数
(_..3) * subscriber2.recieve(*_)                 // 任意个参数
(_..3) * subscriber2.recieve(!"hello")           // 不是"hello"

// 第三个参数任意, 第四个参数不为null, 第五个参数必须是"abc"或者"def"
1 * process.invoke("ls", "-a", _, !null, { ["abc", "def"].contains(it)} )

// 交互测试断言中有变量， 必须用interaction块
interaction {
    def message = "hello"
    1 * subscriber1.receive(message)
}

// 注意:
1 * subscriber.status           // 1 * subsrcriber.getStatus()
1 * subscriber.setStatus("ok")  // setter必须这么写!!!

// 测试invocation order
// 假设发送消息为: "hello" "hello" "goodbye", "hello" "goodbye" "hello"
then:
2 * subscriber.recieve("hello")
1 * subscriber.recieve("goodbye")

then:
1 * subscriber.recieve("hello")
1 * subscriber.recieve("goodbye")

then:
1 * subscriber.recieve("hello")

{% endhighlight %}

严格mocking(Strict Mocking)
{% highlight groovy %}
when:
publisher.publish("hello")

then:
1 * subscriber.receive("hello") // subscriber收到hello消息一次
_ * auditing._                  // 允许与auditing有任意的交互
0 * _                           // 没有其他交互!!
{% endhighlight %}

stub
{% highlight groovy %}

given:
def subscriber = Stub(Subscriber)
Subscriber subscriber = Stub()    // 推荐方式!!!!

subscriber.recieve(_) >> "abc"
subscriber.recieve(_) >>> ["abc", "def"]  // 第一次返回"abc", 第二次返回"def"
subscriber.recieve(_) >> { args -> args[0].size() > 3 "ok" : "fail" }
subscriber.recieve(_) >> { throw new InternalError("ouch") }  // 执行副作用
subscriber.recieve(_) >>> ["ok", "fail", "ok"] >> { throw new InternalError() } >> "ok"   // chained

// 另外一种设置stub的方法!!!
def subscriber = Stub(Subscriber) {
    recieve("message1") >> "ok"
    recieve("message2") >> "fail"
}

{% endhighlight %}

结合mock与stub

当mocking与stubbing同一个方法调用时, 必须在同一个interaction中指定！！！
如下的方式是错误的。 
{% highlight groovy %}
setup:
subscriber.receive("message1") >> "ok"

when:
publisher.send("message1")

then:
1 * subscriber.receive("message1")
{% endhighlight %}
上面的代码执行过程是这样的:
then部分的recieve首先被match, 由于这里并没有指定response, 
这里的return为nul。 setup部分的没有机会match
正确的做法如下:
{% highlight groovy %}
then:
1 * subscriber.recieve("message1") >> "ok"        // "message1"消息收到一次且处理结果是ok
1 * subscriber.recieve("message2") >> "fail"      // "message2"消息收到一次且处理结果是fail
{% endhighlight %}


Spyies 用于对具体对象的部分功能覆盖。 比方说A类有x, y, z三个方法，我们想改变z方法的，但x, y方法保持不变
{% highlight groovy %}

given:
def subscriber = Spy(SubscriberImpl, constructorArgs: ["Fred"])
subscriber.recieve(_) >> { 
  //
  // 调用SubscriberImpl的方法!!! , 这里并没有给callRealMethod传递参数! 
  // 参数被自动加上
  // 如果我们想传递不同的参数给实际方法可以用callRealMethodWithArgs("changeds message")
  //
  String message -> callRealMethod();    
  message.size() > 3 ? "ok":"fail" 
}

{% endhighlight %}

Spy作部分mock(Spy as partial mocks)

{% highlight groovy %}

// persister现在时被测对象!
def persister = Spy(MessagePersister) {
    isPersistable(_) >> true
}

when:
persister.receive(msg")

then:
1 * persister.persist("msg")

{% endhighlight %}

