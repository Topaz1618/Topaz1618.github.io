---
title: "Redis features and application scenarios"
layout: post
date: 2021-12-24 22:48
tag:
 - NoSQL
 - Redis
 - EN
 - 
blog: true
author: Topaz
summary: "MySQL Error 2003 的排查记录"
permalink: MySQL-ERROR-2003
---
<h1 class="title"> MySQL ERROR 2003 报错记录 </h1>
### 问题：
MySQL 远程连接报错
{% highlight html %}
ERROR 2003 (HY000): Can't connect to MySQL server (111)
{% endhighlight %}

### 背景
1.服务器宕机前 MySQL 正常

2.本机登录 MySQL 正常

3.MySQL配置没动过，所以配置也正常

### 排查
1.查看配置文件
 {% highlight html %}
 [root@Topaz ~]# cat /etc/my.cnf
 port = 3306
 bind-address = 0.0.0.0
 #skip-networking
 {% endhighlight %}
会导致远程连不上的几个参数都ok


2.在另一台机器上ping
 {% highlight html %}
 [root@web ~]# ping 10.0.0.129
 	PING 10.0.0.129 (10.0.0.129) 56(84) bytes of data.
 	64 bytes from 10.0.0.129: icmp_seq=1 ttl=64 time=4.14 ms
 {% endhighlight %}
能ping通

3.telnet端口，oops连不上
 {% highlight html %}
 [root@web ~]# telnet 10.0.0.129 3306
 	telnet: connect to address 10.0.0.129: No route to host
 {% endhighlight %}

4.iptables -L 检查防火墙

5.最后确定是防火墙的问题，该删的删
