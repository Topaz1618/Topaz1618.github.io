---
title: "[译] RabbitMQ 6种队列模式详解"
layout: post
date: 2018-04-24 22:48
tag:
 - Learn
blog: true
author: Topaz
summary: "Extra markdown components like highlighted text, side-by-side items, starring/highlighting a blog or project, and embedding gists, videos etc"
permalink: Learning-RabbitMQ-note
---
<h1 class="title"> [译] RabbitMQ 6种队列模式详解 </h1>



<h2> Content </h2>
- [RabbitMQ介绍](#c1)
- [Hello World 模式](#c2)
- [Work Queue 模式](#c3)
- [Publish\Subscribe 模式](#c4)
- [Routing 模式](#c5)
- [Topics 模式](#c6)
- [RPC 模式](#c7)
- [总结](#c8)




<h2 id="c1"> RabbitMQ介绍 </h2>


<h4>特性</h4>
{% highlight raw %}
- 异步消息：支持多种消息协议，消息队列，灵活的路由队列，交换类型
- 方便部署：支持使用BOSH, Chef, Docker 和 Puppet等
- 支持多种语言开发：Java, .NET, PHP, Python, JavaScript, Ruby, Go
- 企业和云：轻量级，云上易部署，可插入身份验证，授权，支持TLS和LDAP
- 管理监控：提供HTTP-API, command line tool 和 UI 来管理监控 RabbitMQ.
- 支持分布式
- 提供多种工具和插件
{% endhighlight %}


<h4>术语</h4>
{% highlight raw %}
- Server(Broker)：接收客户端连接，实现AMQP协议的消息队列和路由功能的进程
- Virtual Host：虚拟主机的概念，类似权限控制组，一个Virtual Host里可以有多个Exchange和Queue,权限控制的最小粒度是Virtual Host
- Exchange：交换机，接收生产者发送的消息，并根据Routing Key将消息路由到服务器中的队列Queue
- ExchangeType：交换机类型决定了路由消息行为，RabbitMQ中有三种类型Exchange，分别是fanout、direct、topic
- Message Queue：消息队列，用于存储还未被消费者消费的消息
- Message：由Header和body组成
 	Header是由生产者添加的各种属性的集合，包括Message是否被持久化、优先级是多少、由哪个Message Queue接收等
 	body是真正需要发送的数据内容
- BindingKey：绑定关键字，将一个特定的Exchange和一个特定的Queue绑定起来
{% endhighlight %}


<h4>RabbitMQ提供以下6种模式</h4>
{% highlight raw %}
- Hello World
- Work Queue
- Publish/Subscribe
- Routing
- Topics
- RPC
{% endhighlight %}
下面对这六种模式进行介绍

<h2 id="c2">简单模式</h2>

<a style="color: #AED6F1" href="https://ai.google/education/#?modal_active=none">[Google 机器学习速成课程]	</a>

<a style="color: #AED6F1" href="https://developers.google.com/machine-learning/crash-course/prereqs-and-prework">[Google 机器学习速成课程 中文版]</a>

<a style="color: #AED6F1" href="https://www.youtube.com/watch?v=ilpFzOPznJk">[数据不足如何用深度学习]</a>

<a style="color: #AED6F1" href="https://mp.weixin.qq.com/s?biz=MzI0ODcxODk5OA==&mid=2247492885&idx=1&sn=d41903ad3f45394eefd12d943a4847f6&chksm=e99ed6ecdee95ffa99804c0afaa21a39a26c097591a2586b7ae205e81d6d9d711389b8c7aa6a&utm_source=tuicool&utm_medium=referral">[2018年机器学习 15大领域 50篇文章]</a>

<a style="color: #AED6F1" href="https://weibo.com/ttarticle/p/show?id=2309404213172029491937">[谷歌机器学习速成课学前预备书单]</a>


<h2 id="c3">Work模式</h2>
<h4>Work 模式 介绍</h4>

<h4><a style="color: #AED6F1"> Demo </a></h4>
生产者：
{% highlight python %}
import pika
import time
connection = pika.BlockingConnection(pika.ConnectionParameters('localhost'))
channel = connection.channel()
channel.queue_declare(queue='hello')
msg = '好冷啊好冷啊 %s'%time.ctime()
channel.basic_publish(exchange='',
					routing_key='hello',
					body=msg,
					properties=pika.BasicProperties(delivery_mode=2,)#消息持久化，重启server数据不消失
					)
print("生产者")
connection.close()
{% endhighlight %}

消费者
{% highlight python %}
import pika
connection = pika.BlockingConnection(pika.ConnectionParameters('localhost'))
channel = connection.channel()
channel.queue_declare(queue='hello')    #声明一个队列，生产者和消费者都要声明一个相同的队列，用来防止万一某一方挂了，另一方能正常运行
def callback(ch,method,properties,body):  #定义一个回调函数，用来接收生产者发送的消息
	print(body.decode())
	time.sleep(20)
	print("method.delivery_tag", method.delivery_tag)
	ch.basic_ack(delivery_tag=method.delivery_tag)  #要显式的发送确认消息,明确的告诉服务器消息被处理了
channel.basic_qos(prefetch_count=1)	#消费者当前消息还没处理完的时候就不要再发新消息了（加上这条就公平分发，配置高的多干）
channel.basic_consume(callback,      #调用回调函数，从队列里取消息
					queue='hello',#指定取消息的队列名
					no_ack=True   #取完一条消息后，不给生产者发送确认消息，默认是False的，即  默认给rabbitmq发送一个收到消息的确认，一般默认即可
					)
print("等待消息")
channel.start_consuming()       #开始循环取消息
{% endhighlight %}

<h2 id="c5">Routing 模式</h2>

<a style="color: #AED6F1" href="https://www.zhihu.com/question/27471510/answer/374935368">[优秀的技术博客]	</a>


<h2 id="c6">Topics 模式</h2>

<a style="color: #AED6F1" href="https://juejin.im/post/5ad4094e6fb9a028d7011069">[tcp http面试指南]</a>

<a style="color: #AED6F1" href="https://github.com/jwasham/coding-interview-university/blob/master/translations/README-cn.md">[Google Interview University 学习手册] </a>


<h2 id="c7">RPC 模式</h2>

<a style="color: #AED6F1" href="https://developers.google.com/edu/c++/getting-started">[Google的C++教程] </a>

<h2 id="c8">总结</h2>

<a style="color: #AED6F1" href="http://rextester.com/l/python3_online_compiler">[在线运行代码] </a>



