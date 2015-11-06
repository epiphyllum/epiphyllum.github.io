---
layout: post
title: "当cron遇到date命令"
description: "cron中的date命令"
category: "bash"
tags: ["bash",  "cron",  "date"]
---
{% include JB/setup %}

{% highlight text %}
今天配置crontab遇到一个很奇怪的问题， 配置如下:
40 * * * * /home/hary/bin/backup.pl `/bin/date +%Y%m%d`
本意是想backup程序执行时, 每次都带上当前的日期作为参数
但这是错误的。 
{% endhighlight %}

正确的做法是
{% highlight bash %}
40 * * * * /home/hary/bin/backup.pl `/bin/date +\%Y\%m\%d`
{% endhighlight %}


<!-- 多说评论框 start -->
<div class="ds-thread" data-thread-key="请将此处替换成文章在你的站点中的ID" data-title="请替换成文章的标题" data-url="请替换成文章的网址"></div>
<!-- 多说评论框 end -->
<!-- 多说公共JS代码 start (一个网页只需插入一次) -->
<script type="text/javascript">
    var duoshuoQuery = {short_name:"epiphyllum"};
    (function() {
        var ds = document.createElement('script');
        ds.type = 'text/javascript';ds.async = true;
        ds.src = (document.location.protocol == 'https:' ? 'https:' : 'http:') + '//static.duoshuo.com/embed.js';
        ds.charset = 'UTF-8';
        (document.getElementsByTagName('head')[0] 
         || document.getElementsByTagName('body')[0]).appendChild(ds);
    })();
</script>
<!-- 多说公共JS代码 end -->

