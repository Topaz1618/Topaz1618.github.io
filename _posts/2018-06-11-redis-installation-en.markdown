---
title: "Redis Installation and Redis Cluster deployment"
layout: post
date: 2017-05-09 22:48
tag:
 - Deployment
 - Redis
 - Installation
 - EN
blog: true
author: Topaz
summary: "Redis Installation by source code，Redis Cluster deployment and Using Redis-CLI with a Redis Cluster "
permalink: reids-installation-en
---
<h1 class="title"> Redis Installation and Redis Cluster deployment </h1>

<h2> Table of Contents </h2>
- [Why Redis](#c1)
- [Install Redis from Source](#c2)
- [Redis Cluster deployment](#c3)
- [Using Redis-CLI with Redis Cluster](#c4)

<h2 id="c1"> Why Redis </h2>
<a style="color: #AED6F1" href="https://www.jianshu.com/p/238372c25669"> ☞ 看这里</a>

<h2 id="c2"> Install Redis from Source </h2>

{% highlight bash %}
1.Downloading the source files & Unpacking
 wget http://download.redis.io/releases/redis-3.2.0.tar.gz
 tar xf redis-3.2.0.tar.gz -C /application/

2.Compiling Redis
 cd /application/redis-3.2.0/
 make && make install

3. Configuring
 cp redis.conf redis.conf.ori  #Backup
 egrep -v "^$|^#" redis.conf.ori >redis.conf #Remove empty lines and comment lines
 sed -i "s#daemonize no#daemonize yes#g" redis.conf #For running in the background
 sed -i "s/127.0.0.1/0.0.0.0/g" redis.conf #Support remote connection

4.Run & Check status
 ./src/redis-server redis.conf
 ps -ef|grep redis
{% endhighlight %}


<h2 id="c3"> Redis Cluster Deployment </h2>

1.Change redis.conf
{% highlight bash %}
 cluster-enabled yes
 cluster-config-file nodes.conf
 cluster-node-timeout 5000
 appendonly yes
{% endhighlight %}

2.Installing dependencies
{% highlight bash %}
 yum install ruby ruby-devel rubygems rpm-build
 gem install redis
{% endhighlight %}

3.Createing path for nodes，Coping the configuration file，Changing port(Impelement by use bash shell)
{% highlight bash %}
 #!/bin/bash
 for i in {7000..7005};do
 	[ ! -d data/$i ] && mkdir data/$i  # Create directory if not existed
 	cp /usr/local/redis-3.2.0/redis.conf  data/$i/ # copy redis.conf to directory of new node
 	sleep 1
 	sed -i "s/7000/$i/g"  data/$i/redis.conf  #replace port
 done
{% endhighlight %}

4.起集群 (还是用 shell 脚本)
{% highlight bash %}
 #!/bin/bash
 ip = 'xxx.xxx.xxx'
 for i in {7000..7005};do
	cd /data/recluster/$i  #切到各节点目录，有必须这样做的理由(可能起不来)，anyway 切就完了。
	rm -f appendonly.aof  dump.rdb  nodes.conf  #删除残留文件，否则集群100%起不来
    /usr/local/redis-3.2.0/src/redis-server ./redis.conf
 done
 /usr/local/redis-3.2.0/src/redis-trib.rb create --replicas 1 $ip:7000 $ip:7001 $ip:7002  $ip:7003 $ip:7004 $ip:7005  #创建集群
{% endhighlight %}


<h2 id="c4"> Redis Cluster commands </h2>

#### 集群节点检查命令
{% highlight bash %}
 [root@Topaz src]# ./redis-trib.rb check 127.0.0.1:7000 #俩ok就是没问题
 [OK] All nodes agree about slots configuration.
 [OK] All 16384 slots covered.
 注意：没加入集群的节点不能用redis-trib.rb检查，单节点检查看下面
{% endhighlight %}


#### 单节点检查命令
{% highlight bash %}
 [root@Topaz src]# redis-cli -h 127.0.0.1 -p 7000 #出现如下效果就没问题儿~~
 127.0.0.1:7000>
{% endhighlight %}

#### 查看集群节点详细信息命令

{% highlight bash %}
 [root@Topaz src]# ./redis-cli -h 127.0.0.1 -p 7003 cluster nodes #随意连个集群端口，就会显示出当前集群内所有节点的信息

 2705c2ed7b4fdb54c114ccafea7d9daaa0926c56 127.0.0.1:7000 master - 0 1471492700496 1 connected 0-5460
 f240c8229fc0ee044d42b03f5c4ce26d4c9517af 127.0.0.1:7004 slave 	2ebe9e2cb72b9103a190afa71e3ca290ad1b6573  01471492699995 5 connected
 ba13cf3bde7f9540bc1531ad0cf803ffe0bf2250 127.0.0.1:7002 master - 0 1471492700996 3 connected 	10923-16383
 2ebe9e2cb72b9103a190afa71e3ca290ad1b6573 127.0.0.1:7001 master - 0 1471492700496 2 connected 5461-10922
 2d053828d63cd96334b9140b408940520ba4bca6 127.0.0.1:7003 myself,slave 2705c2ed7b4fdb54c114ccafea7d9daaa0926c56 00 4 connected
 1ebd5c8e0b4c36a9732c6e8082287f5e2a950045 :0 slave,fail,noaddr ba13cf3bde7f9540bc1531ad0cf803ffe0bf2250  1471491985114 1471491983111 6 disconnected
 {% endhighlight %}

- 显示信息介绍
{% highlight row %}
 第一列：node id
 第二列：Ip Port
 第三列：节点角色(master/slave)
 第四列：角色是slave的，后面接它所属主节点的 node id
{% endhighlight %}

- 节点之间从属关系的查看
{% highlight row %}
 拿 7001 和它的从节点 7004 举例:
  2ebe9e2cb72b9103a190afa71e3ca290ad1b6573 127.0.0.1:7001 master - 0 1471492700496 2 connected 5461-10922
  f240c8229fc0ee044d42b03f5c4ce26d4c9517af 127.0.0.1:7004 slave 2ebe9e2cb72b9103a190afa71e3ca290ad1b6573  0 1471492699995 5 connected
 可以看到7004后面接的 id 和 7001的 node id一致，7004就是7001的从节点。
{% endhighlight %}


#### 添加从节点到集群命令
{% highlight row %}
 /usr/local/redis-3.2.0/src/redis-trib.rb add-node --slave --master-id [主节点node id] 127.0.0.1:7002 [ master ip]:[port]
{% endhighlight %}
