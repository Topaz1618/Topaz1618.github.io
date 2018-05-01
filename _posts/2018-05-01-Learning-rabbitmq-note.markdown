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

> 先决条件：假定RabbitMQ 在本机的标准端口（5672）的上运行。如果使用不同的主机，端口记得调。下面对这六种模式进行介绍


<h2 id="c2">简单模式</h2>

<a style="color: #AED6F1" href="https://ai.google/education/#?modal_active=none">pass</a>



<h2 id="c3">Work模式</h2>
Work Queues（又名Task Queues)，背后的主要思想是避免立即执行资源密集型任务，任务必须进行等待，被封装成消息后发送到队列，后台运行的工作进程将弹出任务并执行，当运行多个生产者时，任务将在他们之间共享。在这种模式下，RabbitMQ会默认把p发的消息依次分发给各个消费者(c),跟负载均衡差不多，可以通过起多个消费者感受下这点。

<h4>Demo </h4>
生产者：
{% highlight python %}
import pika
import time
connection = pika.BlockingConnection(pika.ConnectionParameters('localhost'))
channel = connection.channel()
channel.queue_declare(queue='hello')
msg = '生产者发消息啦 %s'%time.ctime()
channel.basic_publish(exchange='',
	routing_key='hello',
	body=msg,
	properties=pika.BasicProperties(delivery_mode=2,)	#消息持久化，重启server数据不消失
	)
connection.close()
{% endhighlight %}

消费者
{% highlight Python %}
import pika
connection = pika.BlockingConnection(pika.ConnectionParameters('localhost'))
channel = connection.channel()
channel.queue_declare(queue='hello')    #声明一个队列，生产者和消费者都要声明一个相同的队列，用来防止万一某一方挂了，另一方能正常运行
def callback(ch,method,properties,body):  #定义一个回调函数，用来接收生产者发送的消息
	print(body.decode())
	print("method.delivery_tag", method.delivery_tag)
	ch.basic_ack(delivery_tag=method.delivery_tag)  #显式的发送确认消息,明确的告诉服务器消息被处理了
channel.basic_qos(prefetch_count=1)	 #如果消费者未进行ack确认的消息达到这个值，生产者就不再给它发消息了
channel.basic_consume(callback,      #调用回调函数
	queue='hello',	 #指定取消息的队列
	no_ack=False   	 #默认值False，意为开启acknowledge机制，取完一条消息后，给生产者发送确认消息
	)
print("等待消息")
channel.start_consuming()       #循环取消息
{% endhighlight %}

<h4>参数详解 </h4>
上面demo里进行了注释，但还是详细说明下

- 消息持久性:
当 RabbitMQ 退出或崩溃时，队列和消息会被丢失，确保RabbitMQ不丢失队列，设置 durable = True，需要注意的是 RabbitMQ 不允许使用不同的参数重新定义现有的队列，所以声明一个具有不同名称的队列，例如task_queue。
{% highlight row %}
 channel.queue_declare（queue = 'task_queue'，durable = True）
{% endhighlight %}

##### 消息持久性：





<h2 id="c3">Publish\Subscribe 模式</h2>
Publish\Subscribe 模式向多个消费者传递信息





<h2 id="c5">Routing 模式</h2>

<a style="color: #AED6F1" href="https://www.zhihu.com/question/27471510/answer/374935368">[优秀的技术博客]	</a>


<h2 id="c6">Topics 模式</h2>

<a style="color: #AED6F1" href="https://juejin.im/post/5ad4094e6fb9a028d7011069">[tcp http面试指南]</a>

<a style="color: #AED6F1" href="https://github.com/jwasham/coding-interview-university/blob/master/translations/README-cn.md">[Google Interview University 学习手册] </a>


<h2 id="c7">RPC 模式</h2>

<a style="color: #AED6F1" href="https://developers.google.com/edu/c++/getting-started">[Google的C++教程] </a>

<h2 id="c8">总结</h2>

<a style="color: #AED6F1" href="http://rextester.com/l/python3_online_compiler">[在线运行代码] </a>



