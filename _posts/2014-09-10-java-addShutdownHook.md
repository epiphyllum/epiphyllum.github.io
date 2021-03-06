---
layout: post
title: "addShutdownHook与主线程停止"
description: "addShutdownHook"
category: "java"
tags: ["java", "addShutdownHook"]
---
{% include JB/setup %}

如果在我系统中main线程是唯一的前台线程，当前启动了一堆其他服务后， 没有其他事情可干， 
这时候, 需要一个阻塞点。addShutdownHook + syncronized + wait/notify是个不错的手段

{% highlight java %}

public class Main {

    private static volatile boolean running = true;
    private static final Logger logger = LogFactory.getLogger(Main.class);

    public static void main(String[] args[]) {
        logger.debug("Chaos starting..." );

        try {

            // 加载组件
            final List<Component> components = new ArrayList<Component>();
            for(int i = 0; i < args.length; ++i) {
                components.add();
            }

            // 注册shutdown hook -- 退出时可让main退出
            Runtime.getRuntime().addShutdownHook(new Thread() {
                public void run() {
                    synchronized(Main.class) {
                        running = false;
                        Main.class.notify();
                     }
                 }
            });

            // 启动组件
            for( Component component : components) {
                component.start();
                logger.info("Component " + component.getClass().getSimpleName() + " started");
            }
            System.out.println(new SimpleDateFormat("[yyyy-MM-dd HH:mm:ss]").format(new Date()) + "Chaos service Server started!");

        } catch (RuntimeException e) {
            e.printStackTrace();
            logger.error(e.getMessage(), e);
            System.exit(1);
        }

        // 等待结束!
        synchronized(Main.class) {
            while(running) {
                try {
                    Main.class.wait();
                } catch ( Throwable e) {
                }
            }
        }
    }
}

{% endhighlight %}

