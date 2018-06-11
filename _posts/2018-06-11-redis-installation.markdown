---
title: "Redis 集群搭建 "
layout: post
date: 2017-02-24 22:48
tag:
 - Installation
 - Redis
blog: true
author: Topaz
summary: "Extra markdown components like highlighted text, side-by-side items, starring/highlighting a blog or project, and embedding gists, videos etc"
permalink: reids-installation
---
<h1 class="title"> Redis 集群搭建 </h1>

<h2> Table of Contents </h2>
- [为什么选择redis](#c1)
- [Redis 安装](#c2)
- [Redis 集群搭建](#c3)
- [Redis 集群命令](#c4)

<h2 id="c1"> 为什么选择redis </h2>
<a style="color: #AED6F1" href="https://www.jianshu.com/p/238372c25669">看这里</a>

<h2 id="c2"> Redis 安装 </h2>

{% highlight bash %}
1.下载解压
 wget http://download.redis.io/releases/redis-3.2.0.tar.gz
 tar xf redis-3.2.0.tar.gz -C /application/

2.编译安装
 cd /application/redis-3.2.0/
 make && make install

3. 修改配置文件
 cp redis.conf redis.conf.ori  #备份
 egrep -v "^$|^#" redis.conf.ori >redis.conf #去掉空行和注释行
 sed -i "s#daemonize no#daemonize yes#g" redis.conf #后台启动
 sed -i "s/127.0.0.1/0.0.0.0/g" redis.conf #远程连接

4.启动 & 检查进程
 ./src/redis-server redis.conf
 ps -ef|grep redis
{% endhighlight %}


<h2 id="c3"> Redis 集群搭建 </h2>
{% highlight bash %}
1.修改 redis.conf 
 cluster-enabled yes
 cluster-config-file nodes.conf
 cluster-node-timeout 5000
 appendonly yes

2.安装依赖
 yum install ruby ruby-devel rubygems rpm-build
 gem install redis

3. 使用下面的 shell 脚本完成创建节点目录，拷贝配置文件，修改端口。
 #!/bin/bash
 for i in {7000..7005};do
 	[ ! -d data/$i ] && mkdir data/$i  #判断个节点目录是否存在，不在创建
 	cp /usr/local/redis-3.2.0/redis.conf  data/$i/ # redis.conf拷到各节点目录下
 	sleep 1
 	sed -i "s/7000/$i/g"  data/$i/redis.conf  #替换端口
 done

4.使用脚本起服务
 #!/bin/bash
 ip = 'xxx.xxx.xxx'
 for i in {7000..7005};do
	cd /data/recluster/$i  #切到各节点目录，有必须这样做的理由(可能起不来)，anyway 切就完了。
	rm -f appendonly.aof  dump.rdb  nodes.conf  #删除残留文件，否则集群100%起不来
    /usr/local/redis-3.2.0/src/redis-server ./redis.conf 
 done
 /usr/local/redis-3.2.0/src/redis-trib.rb create --replicas 1 $ip:7000 $ip:7001 $ip:7002  $ip:7003 $ip:7004 $ip:7005  #创建集群
{% endhighlight %}


<h2 id="c4"> Redis 集群命令 </h2>

{% highlight bash %}
- 集群节点检查命令，俩ok就是没毛病
 [root@Topaz src]# ./redis-trib.rb check 127.0.0.1:7000
 [OK] All nodes agree about slots configuration.
 [OK] All 16384 slots covered.
 注意：没加入集群的节点不能用redis-trib.rb检查，单节点检查看下面

- 单节点(没加到集群里的)检查，直接连就可以了，出现如下效果就没问题儿~~
 [root@Topaz src]# redis-cli -h 127.0.0.1 -p 7000
 127.0.0.1:7000> 

- 节点node id，从属查看
 [root@Topaz src]# ./redis-cli -h 127.0.0.1 -p 7003 cluster nodes
 2705c2ed7b4fdb54c114ccafea7d9daaa0926c56 127.0.0.1:7000 master - 0 1471492700496 1 connected 0-5460
 f240c8229fc0ee044d42b03f5c4ce26d4c9517af 127.0.0.1:7004 slave 	2ebe9e2cb72b9103a190afa71e3ca290ad1b6573  01471492699995 5 connected
 ba13cf3bde7f9540bc1531ad0cf803ffe0bf2250 127.0.0.1:7002 master - 0 1471492700996 3 connected 	10923-16383
 2ebe9e2cb72b9103a190afa71e3ca290ad1b6573 127.0.0.1:7001 master - 0 1471492700496 2 connected 5461-10922
 2d053828d63cd96334b9140b408940520ba4bca6 127.0.0.1:7003 myself,slave 2705c2ed7b4fdb54c114ccafea7d9daaa0926c56 00 4 connected
 1ebd5c8e0b4c36a9732c6e8082287f5e2a950045 :0 slave,fail,noaddr ba13cf3bde7f9540bc1531ad0cf803ffe0bf2250  1471491985114 1471491983111 6 disconnected
 随便连个集群端口，就会显示出当前集群内所有节点的信息，出来了一堆是不是有点懵逼呢，其实很简单
 第一列：node id
 第二列：Ip Port
 第三列：节点角色(master/slave)
 第四列：角色是slave的，后面接它所属主节点的 node id
 拿 7004 举例： 
	f240c8229fc0ee044d42b03f5c4ce26d4c9517af 127.0.0.1:7004 slave 2ebe9e2cb72b9103a190afa71e3ca290ad1b6573  0 1471492699995 5 connected
 可以看到 7004角色是slave, 后面接的 id 和 7001 的节点id一样，所以7004是7001的从节点，晓得了伐？
 
- 添加从节点到集群
 /usr/local/redis-3.2.0/src/redis-trib.rb add-node --slave --master-id [node id] 127.0.0.1:7002 [ master ip]:[port]
 node id : 主节点的 node id。建议先执行上面的从属查看命令，防止有的主节点没有从节点，有的一堆从节点。
 port：可以是集群内的任意端口

{% endhighlight %}








