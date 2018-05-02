---
title: "云服内存溢出解决"
layout: post
date: 2018-04-24 22:48
tag:
 - 问题
 - 云服
 - 内存
blog: true
author: Topaz
summary: "Extra markdown components like highlighted text, side-by-side items, starring/highlighting a blog or project, and embedding gists, videos etc"
permalink: jvm-memory-overflow
---
<h1 class="title"> 云服 JVM 内存溢出 </h1>


<h2>Content </h2>
- [问题现象](#c1)
- [Disk](#c2)
- [Load](#c3)
- [Memory](#c4)
- [Mysql](#c5)
- [Network](#c6)


<h2 id="c1"> 问题现象 </h2>
P2P应用无法访问，查看日志，报错如下
> 一张图片

解释:“PermGen space的全称是Permanent Generation space，是指内存的永久保存区域OutOfMemoryError: PermGen space。从文字上看就是内存溢出，解决方法是加大内存。为什么会内存溢出，这是由于这块内存主要是被JVM存放Class和Meta信息的，Class在被Load的时候被放入PermGen space区域，它和存放Instance的Heap区域不同,GC(Garbage Collection)不会在主程序运行期对PermGen space进行清理，所以如果你的APP会LOAD很多CLASS的话，就很可能出现PermGen space错误”


<h2 id="c2">解决</h2>
<h4>Step1：增加 manager 模块</h4>
增加webapps/manager模块查看当前运行状态， 修改tomcat-user.xml
{% highlight bash %}
</tomcat-users>
<role rolename="manager-gui"/>
<user username="admin" password="Pwd0df" roles="manager-gui"/>
{% endhighlight %}
<<h4>Step2</h4>
重启tomcat
访问 http://ip:port/manager/status 了解当前状态

> 一张懒得现在添加的图

从图中可以看到当前PermGen最大为82M，当前使用率为94%.接近饱和。

<<h4>Step3 配置修改</h4>
JAVA_OPTS='-server -Xms1024m -Xmx1024m -XX:PermSize=128M -XX:MaxPermSize=256m'

https://blog.csdn.net/eff666/article/details/52071873














