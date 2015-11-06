---
layout: post
title: "vim recipes"
description: "vim技巧"
category: "tools"
tags: ["vim"]
---
{% include JB/setup %}

### 可视模式修改命令

{% highlight markdown %}
|  命令  |  用途 |
| :---   | :---  |
|  v     | 面向字符的可视模式 |
|  V     | 面向行的可视模式 |
|  C-v   | 面向列块的可视模式 |
|  gv    | 重写上次高亮选取 |
|  o     | 切换高亮选取的活动端 |
{% endhighlight %}

### gU{motion} / gu{motion}

    有`<a href='#'>one</a>`这么一个字符串, 我们想吧one变成ONE, 或者想把ONE变成one  
    gUit  one -> ONE  
    guit  ONE -> one  
    我们在执行guit时, vim处在命令模式下, 光标可处在a标签的任意位置  

### inside the tag

    这里it的意思是inside the tag: 也就是在xml tag内部的文本对象  
    再比如  vit 是选择"one"  
    vi"  是选择双引号之内的东西"toselect"  
    vi{  是选择大括号之内的东西{toselect}  
    

