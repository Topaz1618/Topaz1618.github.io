---
title: "[CN] Redis 介绍，应用场景，常见故障的避免和处理"
layout: post
date: 2021-12-24 21:43
tag:
 - NoSQL
 - Redis
 - CN
blog: true
author: Topaz
summary: "Redis Introduction, Application Scenarios, Common Problem Troubleshooting "
permalink: MySQL-ERROR-2003
---
<h1 class="title"> Redis 介绍，应用场景，常见故障的避免和处理 </h1>

<h2> Table of Contents</h2>
- [Redis 介绍](#c1)
- [Redis 特点](#c2)
- [Redis 常见数据类型](#c3)
- [Redis 常见功能和应用场景](#c4)

- [Redis持久化方式](#c6)
- [Redis 缓存和数据库数据的一致性](#c6)
- [Redis 内存淘汰策略及Redis过期键删除策略](#c7)
- [Redis持久化方式](#c6)
- [Redis持久化方式](#c6)

- [缓存穿透，缓存击穿，缓存雪崩](#c5)

- [总结](#c8)

var allowPaste = function(e){
  e.stopImmediatePropagation();
  return true;
};
document.addEventListener('copy', allowPaste, true);
document.addEventListener('paste', allowPaste, true);


<h2 id="c1"> RabbitMQ介绍 </h2>
RabbitMQ是一个消息代理：它接受和转发消息，你可以把它想象成一个邮局：当你把你想要发布的邮件放在邮箱中时，你可以确定邮递员最终将邮件发送给你的收件人。在这个比喻中，邮箱，邮局和邮递员都是RabbitMQ，RabbitMQ和邮局的主要区别在于它处理的不是纸张，而是接收存储和转发二进制消息。

<h4>RabbitMQ 特性</h4>
{% highlight raw %}
- 异步消息：支持多种消息协议，消息队列，灵活的路由队列，交换类型
- 方便部署：支持使用BOSH, Chef, Docker 和 Puppet等
- 支持多种语言开发：Java, .NET, PHP, Python, JavaScript, Ruby, Go
- 企业和云：轻量级，云上易部署，可插入身份验证，授权，支持TLS和LDAP
- 管理监控：提供HTTP-API, command line tool 和 UI 来管理监控 RabbitMQ.
- 支持分布式
- 提供多种工具和插件
{% endhighlight %}


<h4>RabbitMQ 术语</h4>
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


{% highlight raw %}
PS：确保 RabbitMQ 在本机的标准端口 5672 的上运行，如果使用不同的主机和端口注意调整代码。
{% endhighlight %}

<h2 id="c2">Hello word模式</h2>
本教程的第一部分，我们将使用Python编写两个小程序; 发送单个消息的生产者，以及接收消息并将其打印出来的消费者。
工作流程为生产者将消息发送到“hello”队列，消费者接收来自该队列的消息。
<h4>Demo</h4>
生产者
{% highlight python %}
import pika
connection = pika.BlockingConnection(pika.ConnectionParameters(host='localhost'))
channel = connection.channel()
channel.queue_declare(queue = 'hello')
channel.basic_publish(exchange = '',
	routing_key = 'hello',
	body = 'Hello World！' )
print("[x]发送'Hello World！")
connection.close()	#关闭连接,确保退出程序前网络缓冲区被刷新
{% endhighlight %}

消费者
{% highlight python %}
import pika
connection = pika.BlockingConnection(pika.ConnectionParameters(host='localhost'))
channel = connection.channel()
channel.queue_declare(queue = 'hello')

def  callback (ch,method,properties,body):
    print(body.decode())

channel.basic_consume(callback,
    queue= 'hello',
    no_ack = True)
print('正在等待消息。要退出，请按CTRL + C')
channel.start_consuming()
{% endhighlight %}

<h2 id="c3">Work模式</h2>
Work Queues（又名Task Queues)，背后的主要思想是避免立即执行资源密集型任务，Work Queues模式下的任务必须进行等待，被封装成消息后发送到队列，后台运行的工作进程将弹出任务并执行，当运行多个生产者时，任务将在他们之间共享。在这种模式下，RabbitMQ会默认把p发的消息依次分发给各个消费者(c),跟负载均衡差不多，可以通过运行多个消费者感受下这点。

<h4>Demo </h4>
生产者
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
channel.queue_declare(queue='hello')    #声明一个名为 hello的队列
def callback(ch,method,properties,body):  #定义回调函数
	print(body.decode())
	print("method.delivery_tag", method.delivery_tag)
	ch.basic_ack(delivery_tag=method.delivery_tag)  #显式的发送确认消息,明确的告诉服务器消息被处理了
channel.basic_qos(prefetch_count=1)	 #公平分发
channel.basic_consume(callback,      #调用回调函数
	queue='hello',	 #指定取消息的队列
	no_ack=False   	 #消息持久性，默认值False
	)
print("等待消息")
channel.start_consuming()       #循环取消息
{% endhighlight %}

<h4>参数详解</h4>
<h5>消息持久化</h5>
当 RabbitMQ 退出或崩溃时，队列和消息会被丢失，设置 durable = True 确保RabbitMQ不丢失队列，需要注意的是 RabbitMQ 不允许使用不同的参数重新定义现有的队列，所以需要声明一个具有不同名称的队列，例如task_queue。
{% highlight row %}
 channel.queue_declare（queue = 'task_queue'，durable = True）
{% endhighlight %}

<h5>公平分发</h5>
RabbitMQ 在消息进入队列时调度消息，不考虑消费者未确认消息的数量，盲目地将第n条消息分发给第n位消费者。所以会出现一个消费者很忙，另一个消费者空闲的状态，通过 basic.qos方法设置 prefetch_count = 1 解决这个问题。
{% highlight row %}
 channel.basic_qos（prefetch_count = 1）
{% endhighlight %}


<h2 id="c3">Publish\Subscribe 模式</h2>
之前的教程中，work 队列中的每个任务只能传递给一个worker。在这一部分，我们学习的“发布/订阅”模式，能够向多个消费者传递信息。

<h4>工作流程</h4>
1.生产者将消息发送到exchange

2.Exchange接收来自生产者的消息，并将它们推送到队列

3.Exchange 根据其定义的规则对接收到的消息处理
<h4>默认交换</h4>
看到这里你可能会有疑问，为虾米 work模式没有用exchange，队列也能收到消息，因为它用了默认的exchange，如下：
{% highlight python %}
channel.basic_publish(exchange = '',
    routing_key = 'hello',
    body = message)
{% endhighlight %}
<h4>Exchange 类型</h4>
现有的几种 exchange 类型：direct，topic，headers 和 fanout，本节我们要学习的是fanout 类型，它会将收到的所有消息广播到所有已知队队列中。
<h4>列出绑定</h4>
如果想要列出所有的 exchange，使用下面的命令
{% highlight python %}
rabbitmqctl list_bindings
{% endhighlight %}
<h4>Demo </h4>
生产者
{% highlight python %}
import pika
connection = pika.BlockingConnection(pika.ConnectionParameters(host='localhost'))
channel = connection.channel()
channel.exchange_declare(exchange='logs', exchange_type='fanout')	#指定类型
message = "多喝热水"
channel.basic_publish(exchange='logs', routing_key='', body=message)
print(" [x] Sent %r" % message)
connection.close()
{% endhighlight %}

消费者
{% highlight python %}
import pika
connection = pika.BlockingConnection(pika.ConnectionParameters(host='localhost'))
channel = connection.channel()
channel.exchange_declare(exchange='logs', exchange_type='fanout')
result = channel.queue_declare(exclusive=True)	#临时队列
queue_name = result.method.queue
channel.queue_bind(exchange='logs', queue=queue_name)  #关联exchange和队列
print("等待中央空调的问候")

def callback(ch, method, properties, body):
    print('收到噜，看看他是怎么骚的\n ---- %s' %body.decode())
channel.basic_consume(callback, queue=queue_name, no_ack=True)
channel.start_consuming()
{% endhighlight %}

<h4>参数详解</h4>

<h5>临时队列</h5>
订阅发布模式需要监听所有的最新消息，而不是其中一部分或旧消息，需要每次连接到Rabbit都有一个新的空队列，这点可以通过创建临时队列来实现。

1.创建空队列
{% highlight python %}
result = channel.queue_declare()
{% endhighlight %}

2.设置 exclusive=True，消费者关闭连接后删除队列
{% highlight python %}
result = channel.queue_declare(exclusive=True)
{% endhighlight %}

<h2 id="c5">Routing 模式</h2>
上一部分实现了将所有消息广播给所有消费者，本节将在消息广播的基础上，添加一个新的功能，选择性订阅，实现这点可以通过exchange类型中的Direct exchange。

<h4>Direct exchange 介绍</h4>
Direct exchange根据routing Key 判定消息发到哪个队列，使用direct ，我们可以实现仅订阅一部分内容，其背后的路由算法为：消息转发到自身绑定的key和routing key完全匹配的队列。多个队列允许绑定相同的key。

<h4>Demo</h4>
生产者
{% highlight python %}
import pika
import sys
connection = pika.BlockingConnection(pika.ConnectionParameters(host='localhost'))
channel = connection.channel()
channel.exchange_declare(exchange='topaz', exchange_type='direct')
severity = sys.argv[1] if len(sys.argv) > 1 else 'info'
message = ' '.join(sys.argv[1:]) or 'Hello World!'
channel.basic_publish(exchange='topaz',
					  routing_key=severity,
					  body=message)
print(severity, message)
connection.close()
{% endhighlight %}

消费者
{% highlight python %}
import pika
import sys
connection = pika.BlockingConnection(pika.ConnectionParameters(host='localhost'))
channel = connection.channel()
channel.exchange_declare(exchange='topaz',exchange_type='direct')
result = channel.queue_declare(exclusive=True)
queue_name = result.method.queue
severities = sys.argv[1:]
if not severities:
    sys.stderr.write("Usage: %s [info] [warning] [error]\n" % sys.argv[0])
    sys.exit(1)
print('severities',severities)
for severity in severities:
    channel.queue_bind(exchange='topaz',
                       queue=queue_name,
                       routing_key=severity)
print('等，接收绑定key值为 warning 的消息 ~~')
# print('等，接收绑定key值为 error 的消息 ~~')

def callback(ch, method, properties, body):
    print(method.routing_key, body)
channel.basic_consume(callback,
                      queue=queue_name,
                      no_ack=False)
channel.start_consuming()
{% endhighlight %}

<h2 id="c6">Topics 模式</h2>
Direct exchange 的改进仍有其局限性，就是不能根据多个标准进行路由，使用更复杂的Topic exchange可以实现这一点。
<h4>Topic exchange 规则</h4>
{% highlight raw %}
- 发送到 topaic exchange的消息不能有任意routing_key
- 字符列表必须是由点来划分
- 可以是任意字符，但一般命名都与消息相关，命名规范你懂的
- 绑定key还是用熟悉的姿势
- 违反规则的消息将不会匹配任何绑定，并被丢失
- 队列绑定RoutingKey为"#"，topic exchange 接受所有消息，相当于fanout
- 绑定中没有使用" * "和"#"时，topic exchange 和 direct exchange一样
{% endhighlight %}

<h4>Topic exchange 的特殊字符</h4>
{% highlight raw %}
- " * " (star) 匹配一个字符
- "#" (hash) 匹配0个或更多字符
{% endhighlight %}

<h4>Demo</h4>
生产者
{% highlight python %}
import pika
import sys
connection = pika.BlockingConnection(pika.ConnectionParameters(host='localhost'))
channel = connection.channel()
channel.exchange_declare(exchange='topic_logs',exchange_type='topic')
routing_key = sys.argv[1] if len(sys.argv) > 1 else 'anonymous.info'
message = ' '.join(sys.argv[1:]) or 'Hello World!'
channel.basic_publish(exchange='topic_logs',
					routing_key=routing_key,
					body=message)
print("hello 发出一个key为[ %s ]的消息" %message)
connection.close()
{% endhighlight %}

消费者
{% highlight python %}
import pika
import sys
connection = pika.BlockingConnection(pika.ConnectionParameters(host='localhost'))
channel = connection.channel()
channel.exchange_declare(exchange='topic_logs', exchange_type='topic')
result = channel.queue_declare(exclusive=True)
queue_name = result.method.queue
binding_keys = sys.argv[1:]
if not binding_keys:
    sys.stderr.write("Usage: %s [binding_key]...\n" % sys.argv[0])
    sys.exit(1)
for binding_key in binding_keys:
    channel.queue_bind(exchange='topic_logs',
                       queue=queue_name,
                       routing_key=binding_key)
print('等符合key为[ %s ]的消息' %binding_key)

def callback(ch, method, properties, body):
    print('收到噜',method.routing_key, body)


channel.basic_consume(callback,
                      queue=queue_name,
                      no_ack=True)
channel.start_consuming()
{% endhighlight %}

<h2 id="c7">RPC 模式</h2>
前面学习了如何使用work队列在多个worker之间分配任务，但是如果需要在远程机器上运行个函数并等待结果，就需要使用RPC(Remote Procedure Call)模式来实现。在本节中，我们将使用RabbitMQ构建一个RPC系统：包含一个客户端和一个可扩展的RPC服务器。
<h4>使用RabbitMQ构造的rpc system</h4>
{% highlight row %}
- 客户端和一个可以扩展的RPC服务器
- 客户端发送rpc请求消息并阻塞，直到服务器回复响应消息。为了收到响应，客户端要设定reply_to = "随机queue名称"
{% endhighlight %}
<h4>工作流程</h4>
{% highlight row %}
1. 当客户端启动时，它创建一个匿名callback队列
2. 客户端发送消息包含：reply_to(设置 callback 队列)和correlation_id(设置每个请求唯一值)，请求被发送到rpc_queue队列
3. Server端等待队列的请求，请求出现时就工作，并使用reply_to字段中的队列将结果发回给客户端
4. 客户端等待callback队列中的数据，收到消息检查correlation_id 值，与请求中的值correlation_id 相同，就返回对应用程序的响应
{% endhighlight %}
<h4>A note of rpc</h4>
当不知道函数调用是本地函数还是RPC时，会出现问题，滥用RPC可能导致代码不可维护，所以注意以下几点：
{% highlight row %}
- 确保显而易见哪个函数调用是本地的，哪个是远程的
- 记录系统，清除组件之间的依赖关系
- 错误事件处理：RPC服务器长时间停机时，客户端应该如何反应
- 尽量使用异步管道，而不是类似RPC的阻塞，结果被异步推送到下一个计算阶段
{% endhighlight %}
<h4>消息属性</h4>
AMQP 0-9-1 协议定义了14个消息属性，常用的只有以下几个：
{% highlight row %}
- delivery_mode：标记为持久消息（value为2）或transient（任意值）
- content_type：用于描述mime类型的编码 （例如：使用的JSON编码，将此属性设置为：application / json）
- reply_to：命名callback队列
- correlation_id：用于将RPC响应与请求相关联，为每个rpc请求建立queue很低效，可以为每个客户端建立queue
{% endhighlight %}
<h4>Correlation id </h4>
每个RPC请求创建一个回调队列非常低效,所以应该为每个客户端创建一个回调队列，但这引发一个问题，在队列收到响应，会不清楚响应所属的请求，使用correlation_id 为每个请求设置唯一值，回调队列中收到消息时，我们将查看此属性，并基于此属性，将响应与请求进行匹配，如果有未知的 correlation_id值，就会丢弃。
<h4>Demo</h4>
RPC Server
{% highlight python %}
import pika

connection = pika.BlockingConnection(pika.ConnectionParameters(host='localhost'))
channel = connection.channel()
channel.queue_declare(queue='rpc_queue')

def fib(n):
    '''生成斐波那契'''
    if n == 0:
        return 0
    elif n == 1:
        return 1
    else:
        return fib(n - 1) + fib(n - 2)
def on_request(ch, method, props, body):
    '''回调函数'''
    n = int(body)
    print(" [.] fib(%s)" % n)
    response = fib(n)
    ch.basic_publish(exchange='',
                     routing_key=props.reply_to,
                     properties=pika.BasicProperties(correlation_id= props.correlation_id),
                     body=str(response))
    ch.basic_ack(delivery_tag=method.delivery_tag)

channel.basic_qos(prefetch_count=1)
channel.basic_consume(on_request, queue='rpc_queue')
print(" [x] Awaiting RPC requests")
channel.start_consuming()
{% endhighlight %}

RPC Client
{% highlight python %}
import pika
import uuid

class FibonacciRpcClient(object):
    def __init__(self):
        self.connection = pika.BlockingConnection(pika.ConnectionParameters(host='localhost'))  # 创建连接
        self.channel = self.connection.channel()
        result = self.channel.queue_declare(exclusive=True)
        self.callback_queue = result.method.queue
        self.channel.basic_consume(self.on_response, no_ack=True, queue=self.callback_queue)  # 订阅回调队列
    def on_response(self, ch, method, props, body):
        if self.corr_id == props.correlation_id:  # 检查correlation_id
            self.response = body
    def call(self, n):
        '''执行rpc请求'''
        self.response = None
        self.corr_id = str(uuid.uuid4())
        self.channel.basic_publish(exchange='',  # 发送包含correlation_id 和  reply_to的请求消息
                                   routing_key='rpc_queue',
                                   properties=pika.BasicProperties(
                                       reply_to=self.callback_queue,
                                       correlation_id=self.corr_id,
                                   ),
                                   body=str(n))
        while self.response is None:  # 循环等数据
            self.connection.process_data_events()
        return int(self.response)
fibonacci_rpc = FibonacciRpcClient()
print(" [x] Requesting fib(30)")
response = fibonacci_rpc.call(30)
print(" [.] Got %r" % response)
{% endhighlight %}
