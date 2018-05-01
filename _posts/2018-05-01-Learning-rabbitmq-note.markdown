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
<div style="color: #AED6F1">
<p>异步消息:支持多种消息协议，消息队列，灵活的路由队列，交换类型</p>
<p>方便部署：支持使用BOSH, Chef, Docker 和 Puppet等</p>
<p>支持多种语言开发：Java, .NET, PHP, Python, JavaScript, Ruby, Go</p>
<p>企业和云：轻量级，云上易部署，可插入身份验证，授权，支持TLS和LDAP</p>
<p>管理监控：提供HTTP-API, command line tool 和 UI 来管理监控 RabbitMQ.</p>
<p>支持分布式</p>
<p>提供多种工具和插件</p>
</div>


<h4>术语</h4>
- Server(Broker):接收客户端连接，实现AMQP协议的消息队列和路由功能的进程；
- Virtual Host：虚拟主机的概念，类似权限控制组，一个Virtual - Host里可以有多个Exchange和Queue,权限控制的最小丽都是Virtual Host；
- Exchange:交换机，接收生产者发送的消息，并根据Routing Key将消息路由到服务器中的队列Queue。
- ExchangeType:交换机类型决定了路由消息行为，RabbitMQ中有三种类型Exchange，分别是fanout、direct、topic；
- Message Queue：消息队列，用于存储还未被消费者消费的消息；
- Message：由Header和body组成，Header是由生产者添加的各种属性的集合，包括Message是否被持久化、优先级是多少、由哪个Messag- e Queue接收等；body是真正需要发送的数据内容；
- BindingKey：绑定关键字，将一个特定的Exchange和一个特定的Queue绑定起来。


<h4>RabbitMQ提供以下6种模式</h4>
<div style="color: #AED6F1">
- Hello World
- Work Queue
- Publish/Subscribe
- Routing
- Topics
- RPC
</div>






<a style="color: #AED6F1" href="https://zhuanlan.zhihu.com/p/35195885">[GitHub 热门的人工智能和数据科学项目] </a>


<h2 id="c2">简单模式</h2>

<a style="color: #AED6F1" href="https://ai.google/education/#?modal_active=none">[Google 机器学习速成课程]	</a>

<a style="color: #AED6F1" href="https://developers.google.com/machine-learning/crash-course/prereqs-and-prework">[Google 机器学习速成课程 中文版]</a>

<a style="color: #AED6F1" href="https://www.youtube.com/watch?v=ilpFzOPznJk">[数据不足如何用深度学习]</a>

<a style="color: #AED6F1" href="https://mp.weixin.qq.com/s?biz=MzI0ODcxODk5OA==&mid=2247492885&idx=1&sn=d41903ad3f45394eefd12d943a4847f6&chksm=e99ed6ecdee95ffa99804c0afaa21a39a26c097591a2586b7ae205e81d6d9d711389b8c7aa6a&utm_source=tuicool&utm_medium=referral">[2018年机器学习 15大领域 50篇文章]</a>

<a style="color: #AED6F1" href="https://weibo.com/ttarticle/p/show?id=2309404213172029491937">[谷歌机器学习速成课学前预备书单]</a>


<h2 id="c3">Work模式</h2>

<a style="color: #AED6F1" href="https://courses.csail.mit.edu/6.042/spring18/mcs.pdf">[MIT 计算机科学的数学理论 教学课件] </a> 


<h2 id="c4">Publish\Subscribe模式</h2>
<a style="color: #AED6F1" href="https://stackoverflow.com/questions/1711/what-is-the-single-most-influential-book-every-programmer-should-read">[每个程序员都该读的书]</a>


<h2 id="c5">Routing 模式</h2>

<a style="color: #AED6F1" href="https://www.zhihu.com/question/27471510/answer/374935368">[优秀的技术博客]	</a>


<h2 id="c6">Topics 模式</h2>

<a style="color: #AED6F1" href="https://juejin.im/post/5ad4094e6fb9a028d7011069">[tcp http面试指南]</a>

<a style="color: #AED6F1" href="https://github.com/jwasham/coding-interview-university/blob/master/translations/README-cn.md">[Google Interview University 学习手册] </a>


<h2 id="c7">RPC 模式</h2>

<a style="color: #AED6F1" href="https://developers.google.com/edu/c++/getting-started">[Google的C++教程] </a>

<h2 id="c8">总结</h2>

<a style="color: #AED6F1" href="http://rextester.com/l/python3_online_compiler">[在线运行代码] </a>



