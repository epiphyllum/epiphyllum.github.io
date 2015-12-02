---
layout: post
title: "Spring StringUtils总结"
description: "Spring StringUtils"
category: "java"
tags: ["java", "addShutdownHook"]
---
{% include JB/setup %}


我们经常会对字符串进行操作，spring已经实现了常用的处理功能。我们可以使用org.springframework.util.StringUtils 工具类帮我们处理字符串。
工具类整理如下：

## 是否有长度
{% highlight java %}
StringUtils.hasLength(null) = false    // null是没有长度
StringUtils.hasLength("") = false      // ""也是没有长度的
StringUtils.hasLength(" ") = true
StringUtils.hasLength("Hello") = true
{% endhighlight %}

## 是否有字符 
{% highlight java %}
StringUtils.hasText(null) = false   // null, "", 以及" "都认为没有字符!!!
StringUtils.hasText("") = false
StringUtils.hasText(" ") = false
StringUtils.hasText("12345") = true
StringUtils.hasText(" 12345 ") = true
{% endhighlight %}

## 是否包含空白字符
{% highlight java %}
StringUtils.containsWhitespace(null)=false     // null 不含
StringUtils.containsWhitespace("")=false       // ""也不含
StringUtils.containsWhitespace("a")=false
StringUtils.containsWhitespace("abc")=false
StringUtils.containsWhitespace("abc")=false
StringUtils.containsWhitespace(" ")=true
StringUtils.containsWhitespace(" a")=true
StringUtils.containsWhitespace("abc ")=true
StringUtils.containsWhitespace("a b")=true
StringUtils.containsWhitespace("a  b")
{% endhighlight %}

去除前后的空白字符
{% highlight java %}
StringUtils.trimWhitespace(null)=null;
StringUtils.trimWhitespace("")="";
StringUtils.trimWhitespace(" ")="";
StringUtils.trimWhitespace("/t")="";
StringUtils.trimWhitespace(" a")="a";
StringUtils.trimWhitespace("a ")="a";
StringUtils.trimWhitespace(" a ")="a";
StringUtils.trimWhitespace(" a b ")="a b";
{% endhighlight %}

去除前的空白字符
{% highlight java %}
StringUtils.trimLeadingWhitespace(null)=null;
StringUtils.trimLeadingWhitespace("")="";
StringUtils.trimLeadingWhitespace(" ")="";
StringUtils.trimLeadingWhitespace("/t")="";
StringUtils.trimLeadingWhitespace(" a")="a";
StringUtils.trimLeadingWhitespace("a ")="a ";
StringUtils.trimLeadingWhitespace(" a ")="a ";
StringUtils.trimLeadingWhitespace(" a b ")="a b "
StringUtils.trimLeadingWhitespace(" a b  c ")="a b  c "
{% endhighlight %}

## 去除后的空白字符
{% highlight java %}
StringUtils.trimTrailingWhitespace(null)=null;
StringUtils.trimTrailingWhitespace(" ")="";
StringUtils.trimTrailingWhitespace("/t")="";
StringUtils.trimTrailingWhitespace("a ")="a";
StringUtils.trimTrailingWhitespace(" a")=" a";
StringUtils.trimTrailingWhitespace(" a ")=" a";
StringUtils.trimTrailingWhitespace(" a b ")=" a b";
StringUtils.trimTrailingWhitespace(" a b  c ")=" a b  c";
{% endhighlight %}

## 去除所有的空白字符
{% highlight java %}
StringUtils.trimAllWhitespace("")="";
StringUtils.trimAllWhitespace(" ")="";
StringUtils.trimAllWhitespace("/t")="";
StringUtils.trimAllWhitespace(" a")="a";
StringUtils.trimAllWhitespace("a ")="a";
StringUtils.trimAllWhitespace(" a ")="a";
StringUtils.trimAllWhitespace(" a b ")="ab";
StringUtils.trimAllWhitespace(" a b  c "="abc";
{% endhighlight %}

## 统计一个子字符串在字符串出现的次数
{% highlight java %}
StringUtils.countOccurrencesOf(null, null) == 0;
StringUtils.countOccurrencesOf("s", null) == 0;
StringUtils.countOccurrencesOf(null, "s") == 0;
StringUtils.countOccurrencesOf("erowoiueoiur", "WERWER") == 0;
StringUtils.countOccurrencesOf("erowoiueoiur", "x")=0;
StringUtils.countOccurrencesOf("erowoiueoiur", " ") == 0;
StringUtils.countOccurrencesOf("erowoiueoiur", "") == 0;
StringUtils.countOccurrencesOf("erowoiueoiur", "e") == 2;
StringUtils.countOccurrencesOf("erowoiueoiur", "oi") == 2;
StringUtils.countOccurrencesOf("erowoiueoiur", "oiu") == 2;
StringUtils.countOccurrencesOf("erowoiueoiur", "oiur") == 1;
StringUtils.countOccurrencesOf("erowoiueoiur", "r") == 2;
{% endhighlight %}

## 字符串替换
{% highlight java %}
String inString = "a6AazAaa77abaa";
String oldPattern = "aa";
String newPattern = "foo";
// Simple replace
String s = StringUtils.replace(inString, oldPattern, newPattern);
s.equals("a6AazAfoo77abfoo")=true;

// 如果不匹配就不会删除
s = StringUtils.replace(inString, "qwoeiruqopwieurpoqwieur", newPattern);
s.equals(inString)=true
s = StringUtils.replace(inString, oldPattern, null);
s.equals(inString)=true

// Null old pattern: should ignore
s = StringUtils.replace(inString, null, newPattern);
s.equals(inString)=true
{% endhighlight %}

## 删除字符串
{% highlight java %}
String inString = "The quick brown fox jumped over the lazy dog";
String noThe = StringUtils.delete(inString, "the");
noThe.equals("The quick brown fox jumped over  lazy dog")=true;
String nohe = StringUtils.delete(inString, "he");
nohe.equals("T quick brown fox jumped over t lazy dog")=true;
String nosp = StringUtils.delete(inString, " ");
nosp.equals("Thequickbrownfoxjumpedoverthelazydog")=true;
String killEnd = StringUtils.delete(inString, "dog");
killEnd.equals("The quick brown fox jumped over the lazy ")=true;
String mismatch = StringUtils.delete(inString, "dxxcxcxog");
mismatch.equals(inString)=true;
{% endhighlight %}

## 删除任何字符
{% highlight java %}
String inString = "Able was I ere I saw Elba";

String res = StringUtils.deleteAny(inString, "I");
res.equals("Able was  ere  saw Elba")=true;
res = StringUtils.deleteAny(inString, "AeEba!");
res.equals("l ws I r I sw l")=true;
String mismatch = StringUtils.deleteAny(inString, "#@$#$^");
mismatch.equals(inString)=true;
{% endhighlight %}

## quote字符串
{% highlight java %}
assertEquals("'myString'", StringUtils.quote("myString"));
assertEquals("''", StringUtils.quote(""));
assertNull(StringUtils.quote(null));
{% endhighlight %}

## 首字母大小写
{% highlight java %}
StringUtils.capitalize(str)  
StringUtils.uncapitalize(str)
{% endhighlight %}

## 获取字符串文件名和扩展名, 如:"mypath/myfile.txt" -> myfile.txt
{% highlight java %}
StringUtils.getFilename("myfile").equals("myfile") = true;
StringUtils.getFilename("mypath/myfile").equals("myfile") = true;
StringUtils.getFilename("mypath/myfile").equals("myfile") = true;
StringUtils.getFilename("myfile.txt").equals("myfile.txt") = true;
StringUtils.getFilename("mypath/myfile.txt").equals("myfile.txt") = true;
{% endhighlight %}

## 获取字符串扩展名, 以.分隔
{% highlight java %}
StringUtils.getFilenameExtension("myfile") = null;
StringUtils.getFilenameExtension("myPath/myfile") = null;
StringUtils.getFilenameExtension("myfile.").equals("") = true;
StringUtils.getFilenameExtension("myPath/myfile.").equals("") = true;
StringUtils.StringUtils.getFilenameExtension("myfile.txt").equals("txt") = true;
StringUtils.getFilenameExtension("mypath/myfile.txt").equals("txt") = true;
{% endhighlight %}

## 舍去文件名扩展名
{% highlight java %}
StringUtils.stripFilenameExtension(null)=true;
StringUtils.stripFilenameExtension("").equals("") = true;
StringUtils.stripFilenameExtension("myfile").equals("myfile") = true;
StringUtils.stripFilenameExtension("mypath/myfile").equals("mypath/myfile") = true;
StringUtils.stripFilenameExtension("myfile.").equals("myfile")=true;
StringUtils.stripFilenameExtension("mypath/myfile.").equals("mypath/myfile") = true;
StringUtils.stripFilenameExtension("mypath/myfile.").equals("mypath/myfile") = true;
StringUtils.stripFilenameExtension("myfile.txt").equals("myfile") = true;
StringUtils.stripFilenameExtension("mypath/myfile.txt").equals("mypath/myfile") = true
{% endhighlight %}

