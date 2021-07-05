---
title: "爬虫之 Scrapy 框架"
layout: post
date: 2018-02-24 22:48
tag:
 - 爬虫
blog: true
author: Topaz
summary: "BeautifulSoup Xpath"
permalink: Spiders-Scrapy-01
---
<h1 class="title"> 爬虫之 Scrapy 框架 </h1>

<h2> Table of Contents </h2>
- [Scrapy 简介](#c1)
- [主要组件](#c2)
- [工作流程](#c3)
- [安装](#c4)
- [基本命令](#c5)
- [HtmlXpathSelector](#c6)
- [写个基于 Scrapy 的爬虫项目](#c7)
- [Scrapy 自定义](#c8)

<a style="color: #AED6F1" href="https://topaz1618.github.io/Spiders-Scrapy-02"> ☞ Scrapy 文件详解</a>

<h2 id="c1"> Scrapy 简介 </h2>
Scrapy是一个为了爬取网站数据，提取结构性数据而编写的应用框架，使用Twisted异步网络库处理网络通讯,爬取网站数据，提取结构性数据的应用框架，可以应用在数据挖掘，信息处理，监测和自动化测试或存储历史数据等一系列的程序中	


<h2 id="c2"> 主要组件 </h2>
{% highlight raw %}
 - 引擎(Scrapy)：用来处理整个系统的数据流处理, 触发事务(框架核心)
 - 调度器(Scheduler)：用来接受引擎发过来的请求, 压入队列中, 并在引擎再次请求的时候返回.  可以想像成一个URL（抓取网页的网址或者说是链接）的优先队列, 由它来决定下一个要抓取的网址是什么, 同时去除重复的网址
 - 下载器(Downloader)：用于下载网页内容, 并将网页内容返回给蜘蛛(Scrapy下载器是建立在twisted这个高效的异步模型上的)	
 - 爬虫(Spiders)：爬虫是主要干活的, 用于从特定的网页中提取自己需要的信息, 即所谓的实体(Item) 。用户也可以从中提取出链接,让Scrapy继续抓取下一个页面
 - 项目管道(Pipeline)：负责处理爬虫从网页中抽取的实体，主要的功能是持久化实体、验证实体的有效性、清除不需要的信息。当页面被 爬虫解析后，将被发送到项目管道，并经过几个特定的次序处理数据
 - 下载器中间件(Downloader Middlewares) ：位于Scrapy引擎和下载器之间的框架，主要是处理Scrapy引擎与下载器之间的请求及响应
 - 爬虫中间件(Spider Middlewares)：介于Scrapy引擎和爬虫之间的框架，主要工作是处理蜘蛛的响应输入和请求输出
 - 调度中间件(Scheduler Middewares)：介于Scrapy引擎和调度之间的中间件，从Scrapy引擎发送到调度的请求和响应
{% endhighlight %}

<h2 id="c3"> 工作流程 </h2>
{% highlight raw %}
 1. 引擎从调度器中取出一个链接(URL)用于接下来的抓取
 2. 引擎把URL封装成一个请求(Request)传给下载器
 3. 下载器把资源下载下来，并封装成应答包(Response)
 4. 爬虫解析Response
 5. 解析出实体（Item）,则交给实体管道进行进一步的处理
 6. 解析出的是链接（URL）,则把URL交给调度器等待抓取
{% endhighlight %}

<h2 id="c4"> 安装 </h2>

#### Linux & Mac
{% highlight python %}
 pip3 install scrapy
{% endhighlight %}

#### Windows
{% highlight python %}
 pip3 install wheel

 - 下载twisted 
 http://www.lfd.uci.edu/~gohlke/pythonlibs/#twisted

 - 进入下载目录
 pip3 install Twisted-17.5.0-cp35-cp35m-win_amd64.whl

 - 安装 scrapy
 pip3 install scrapy

 - 下载并安装pywin32
 https://sourceforge.net/projects/pywin32/files/
{% endhighlight %}

<h2 id="c5"> 基本命令</h2>
{% highlight python %}
 scrapy startproject [项目名称]		 # 创建项目
 scrapy genspider [-t template] <name> <domain>		 #创建爬虫应用
 scrapy list	 #展示爬虫应用列表
 scrapy crawl [爬虫应用名称]  #运行单独爬虫应用
 scrapy genspider -l	 #查看所有命令
 scrapy genspider -d [模板名称]  #查看模板命令
{% endhighlight %}


<h2 id="c6"> HtmlXpathSelector </h2>

#### 简介
 HtmlXpathSelector 是 Scrapy 自有的用于处理HTML文档的选择器，被称为XPath选择器（或简称为“选择器”），是因为它们“选择”由XPath表达式指定的HTML文档的某些部分。

#### 其它选择器
- BeautifulSoup：非常流行，缺点：速度很慢
- lxml：基于 ElementTree 的XML解析库（也解析HTML ）

#### 应用
{% highlight python %}
 from scrapy.selector import Selector, HtmlXPathSelector
 from scrapy.http import HtmlResponse
 html = """
  	<!DOCTYPE html>
 		<html>
 		<head lang="en">
 			<meta charset="UTF-8">
 			<title></title>
 		</head>
 		<body>
 			<ul>
 				<li class="item-"><a id='i12' href="link.html">first item</a></li>
 				<li class="item-0"><a id='i2' href="llink.html">first item</a></li>
 				<li class="item-1"><a href="llink2.html">second item<span>vv</span></a></li>
 			</ul>
 			<div><a href="llink2.html">second item</a></div>
 		</body>
 	</html>
 """
 response = HtmlResponse(url='http://example.com', body=html,encoding='utf-8')
 hxs = Selector(response)  

 #取出所有a标签
 hxs = Selector(response=response).xpath('//a') 

 #取出所有有id属性的a标签
 hxs = Selector(response=response).xpath('//a[@id]')  

 #取出开头为link的href标签
 hxs = Selector(response=response).xpath('//a[starts-with(@href,"link")]' )

 #取出链接包含link的href标签
 hxs = Selector(response=response).xpath('//a[contains(@href, "link")]')

 #正则，取出i开头后边是数字的
 hxs = Selector(response=response).xpath('//a[re:test(@id, "i\d+")]')    

 hxs = Selector(response=response).xpath('//a[re:test(@id, "i\d+")]/text()').extract()
 hxs = Selector(response=response).xpath('//a[re:test(@id, "i\d+")]/@href').extract() 
 hxs = Selector(response=response).xpath('/html/body/ul/li/a/@href').extract() 
 hxs = Selector(response=response).xpath('//body/ul/li/a/@href').extract_first()

 #参考：https://doc.scrapy.org/en/0.12/topics/selectors.html
{% endhighlight %}


<h2 id="c7"> 写个基于 Scrapy 的爬虫项目 </h2>

#### 项目结构
{% highlight raw %}
 cutetopaz/
 	scrapy.cfg				#项目的主配置信息（真正爬虫相关的配置信息在settings.py文件中）
 	cutetopaz/				
 		__init__.py
 		items.py			#设置数据存储模板，用于结构化数据 如：Django的Model
 		pipelines.py		#数据处理行为 如：一般结构化的数据持久化
 		settings.py			#配置文件 如：递归的层数、并发数，延迟下载等
 		spiders/			#爬虫目录 如：创建文件，编写爬虫规则
 			__init__.py
 			xiaohuar.py		#爬虫文件,一般正常人以网站域名命名
{% endhighlight %}

#### xiaohuar.py
{% highlight python %}
 import scrapy
 import hashlib
  scrapy.selector import Selector
  scrapy.http.request import Request
  scrapy.http.cookies import CookieJar
  scrapy import FormRequest
  ..items import XiaoHuarItem
 
 class XiaoHuarSpider(scrapy.Spider):
 	name = "hira"
 	allowed_domains = ["xiaohuar.com"]
 	start_urls = ["http://www.xiaohuar.com/list-1-1.html",]
 	has_request_set = {}
 	def parse(self, response):
 		hxs = Selector(response)
 		items = hxs.select('//div[@class="item_list infinite_scroll"]/div')
 		for item in items:
 			src = item.xpath('.//div[@class="img"]/a/img/@src').extract_first()
 			name = item.xpath('.//div[@class="img"]/span/text()').extract_first() 
 			school = item.xpath('.//div[@class="img"]/div[@class="btns"]/a/text()').extract_first()    	#==>广西大学
 			url = "http://www.xiaohuar.com%s" % src
 			obj = XiaoHuarItem(name=name,school=school, url=url)
 			yield obj
 		urls = hxs.xpath('//a[re:test(@href, "http://www.xiaohuar.com/	list-1-\d+.html")]/@href').extract()    #拿到所有页面
 		for url in urls:
 			print("here is url:",url)
 			key = self.md5(url)
 			print('key!!!!!',key)
 			if key in self.has_request_set:
 				# print('你要的大字典',self.has_request_set)
 				pass
 			else:
 				self.has_request_set[key] = url
 				req = Request(url=url,method='GET',callback=self.parse)
 				yield req
 	@staticmethod
 	def md5(val):
 		ha = hashlib.md5()
 		ha.update(bytes(val, encoding='utf-8'))
 		key = ha.hexdigest()
 		return key
{% endhighlight %}

#### items.py
{% highlight python %}
 import scrapy
 class CutetopazItem(scrapy.Item):
 	# define the fields for your item here like:
 	# name = scrapy.Field()
 	pass
 
 class XiaoHuarItem(scrapy.Item):
 	name = scrapy.Field()
 	school = scrapy.Field()
 	url = scrapy.Field()	
{% endhighlight %}

#### pipelines.py
{% highlight python %}
 import json
 import os
 import requests
 class JsonPipeline(object):
 	'''按照settings.py里的顺序，先执行这个'''
 	def __init__(self):
 		self.file = open('xiaohua.txt', 'w')
 	def process_item(self, item, spider):
 		v = json.dumps(dict(item), ensure_ascii=False)  #item ==> <class 'cutetopaz.items.XiaoHuarItem'>  dict(item) ==> <class 'dict'> ，肉眼看的话item就是个字典啊，去掉dict()报错is not JSON serializable
 		self.file.write(v)
 		self.file.write('\n')
 		self.file.flush()
 		return item     #返回一个json序列化后的item
 class FilePipeline(object):
 	def __init__(self):
 		if not os.path.exists('imgs'):      #创建个文件夹
 			os.makedirs('imgs')
 	def process_item(self, item, spider):
 		response = requests.get(item['url'], stream=True)
 		print('看看',response.__attrs__)
 		file_name = '%s_%s.jpg' % (item['name'], item['school'])
 		with open(os.path.join('imgs', file_name), mode='wb') as f:
 			f.write(response.content)
 		return item	
{% endhighlight %}

#### settings.py
{% highlight python %}
 ITEM_PIPELINES = {
 	'cutetopaz.pipelines.JsonPipeline': 100,
 	'cutetopaz.pipelines.FilePipeline': 300,
 }
{% endhighlight %} 

<h2 id="c8"> Scrapy 自定义 </h2>

#### 自定制命令
{% highlight raw %}
 1.在spiders同级创建任意目录，如：commands
 2.在其中创建 crawlall.py 文件 （此处文件名就是自定义的命令）
 3.settings.py 中添加配置 COMMANDS_MODULE = '项目名称.目录名称'
 4.项目目录中执行命令：scrapy crawlall
{% endhighlight %} 

代码：
{% highlight python %}
 from scrapy.commands import ScrapyCommand
 from scrapy.utils.project import get_project_settings
 class Command(ScrapyCommand):
 	requires_project = True
 	def syntax(self):
 		return '[options]'
 	def short_desc(self):
 		return 'Runs all of the spiders'
 	def run(self, args, opts):
 		spider_list = self.crawler_process.spiders.list()
 		for name in spider_list:
 			self.crawler_process.crawl(name, **opts.__dict__)
 		self.crawler_process.start()
{% endhighlight %} 		

#### 自定义扩展								
自定义扩展时，利用信号在指定位置注册制定操作
{% highlight python %}
 from scrapy import signals
 class MyExtension(object):
 	def __init__(self, value):
 		self.value = value			
 	@classmethod
 	def from_crawler(cls, crawler):
 		val = crawler.settings.getint('MMMM')
 		ext = cls(val)			
 		crawler.signals.connect(ext.spider_opened, signal=signals.spider_opened)
 		crawler.signals.connect(ext.spider_closed, signal=signals.spider_closed)
 		return ext
 	def spider_opened(self, spider):
 		print('open')		
 	def spider_closed(self, spider):
 		print('close')	
{% endhighlight %}

#### 避免重复访问
Scrapy 默认使用 scrapy.dupefilter.RFPDupeFilter 进行去重，相关配置有
{% highlight raw %}
 DUPEFILTER_CLASS = 'scrapy.dupefilter.RFPDupeFilter'
 DUPEFILTER_DEBUG = False
 JOBDIR = "保存范文记录的日志路径，如：/root/"  # 最终路径为 /root/requests.seen
{% endhighlight %} 












