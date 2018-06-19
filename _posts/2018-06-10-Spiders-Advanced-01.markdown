---
title: "爬虫性能之同步异步耗时对比"
layout: post
date: 2018-02-24 22:48
tag:
 - 爬虫
blog: true
author: Topaz
summary: "单进程单线程 多线程 多进程 异步"
permalink: Spiders-advanced-01
---
<h1 class="title"> 爬虫性能之同步异步耗时对比 </h1>

<h2> Table of Contents </h2>
- [同步耗时](#c1)
- [异步 IO 耗时](#c2)

<h2 id="c1"> 同步耗时 </h2>
在编写爬虫时，性能的消耗主要在IO请求中，在单进程单线程模式下请求url必然会引起等待，从而使得请求整体变慢

#### 单进程单线程模式
{% highlight python %}
 实例1：耗时 ==> 19.061163187026978
 import requests,time
 current_time = time.time()
 a = requests.get('http://www.cnblogs.com/wupeiqi/articles/6229292.html')
 b = requests.get('https://h5.qichedaquan.com/jike/?jkcx=0&channel=jingxiyuean')
 print(a.text)
 print(b.text)
 print(time.time()- current_time) 

 实例2：耗时 ==> 19.067904472351074
 import requests,time
 time1 = time.time()
 def get_url(url):
 	a = requests.get(url)
 	return a.text
 url_list = ['http://www.cnblogs.com/wupeiqi/articles/6229292.html',
 			'https://h5.qichedaquan.com/jike/?jkcx=0&channel=jingxiyuean']
 for i in url_list:
 	mess = get_url(i)
 	print(mess)
 	print(time.time()-time1) 
{% endhighlight %}

#### 多线程
耗时 ==> 9.92071795463562
{% highlight python %}
 import requests
 import threading
 import time
 def get_url(url):
 	a = requests.get(url)
 	print(a.text)
 	print(time.time() - mytime)     
 url_list = ['http://www.cnblogs.com/wupeiqi/articles/6229292.html',
 			'https://h5.qichedaquan.com/jike/?jkcx=0&channel=jingxiyuean']
 if __name__ == '__main__':
 	mytime = time.time()
 	for url in url_list:
 		t = threading.Thread(target=get_url,args=(url,))
 		t.start()
{% endhighlight %}

#### 线程池 		
耗时 ==> 10.068973302841187
{% highlight python %}
 from concurrent.futures import ThreadPoolExecutor
 import requests
 import time
 def fetch_async(url):
 	response = requests.get(url)
 	return response.text
 def callback(future):
 	print(future.result())
 url_list = ['http://www.cnblogs.com/wupeiqi/articles/6229292.html',
 			'https://h5.qichedaquan.com/jike/?jkcx=0&channel=jingxiyuean']
 pool = ThreadPoolExecutor(2)	#创建个容量为2的线程池
 time1 = time.time()
 for url in url_list:
 	v = pool.submit(fetch_async, url)
 	v.add_done_callback(callback)
 pool.shutdown(wait=True)
 print(time.time()-time1)
 从Python3.2开始，标准库为我们提供了concurrent.futures模块，它提供了ThreadPoolExecutor和ProcessPoo
lExecutor两个类，对编写线程池/进程池提供了直接的支持
{% endhighlight %}

#### 多进程
耗时 ==> 10.076193809509277
{% highlight python %}
 from multiprocessing import Process
 import requests
 import time
 def fetch_async(url):
 	response = requests.get(url)
 	print(response)
 	print(time.time() - time1)
 url_list = ['http://www.cnblogs.com/wupeiqi/articles/6229292.html',
 			'https://h5.qichedaquan.com/jike/?jkcx=0&channel=jingxiyuean']
 time1 = time.time()
 if __name__ == '__main__':
 	for url in url_list:
 		p = Process(target=fetch_async, args=(url,))
 		p.start()
{% endhighlight %}

#### 进程池
耗时 ==> 11.25045657157898
{% highlight python %}
 from concurrent.futures import ProcessPoolExecutor
 import requests
 import time
 def fetch_async(url):
 	response = requests.get(url)
 	return response
 def callback(future):
 	print(future.result())
 url_list = ['http://www.cnblogs.com/wupeiqi/articles/6229292.html',
 			'https://h5.qichedaquan.com/jike/?jkcx=0&channel=jingxiyuean']
 pool = ProcessPoolExecutor(2)
 time1 = time.time()
 if __name__ == '__main__':
 	for url in url_list:
 		v = pool.submit(fetch_async, url)
 		v.add_done_callback(callback)
 	pool.shutdown(wait=True)
 	print(time.time() - time1)
{% endhighlight %}


<h2 id="c2"> 异步 IO 耗时 </h2>

#### Asyncio
简介
{% highlight raw %}
 asyncio是Python 3.4版本引入的标准库，直接内置了对异步IO的支持,asyncio的编程模型就是一个消息循环，从asyncio模块中直接获取一个EventLoop的引用，然后把需要执行的协程扔到EventLoop中执行，就实现了异步IO
{% endhighlight %}

Demo：耗时 ==> 0.6783857345581055
{% highlight python %}
 import asyncio
 import time
 @asyncio.coroutine
 def wget(host, url='/'):
 	print('路径： %s%s' % (host, url))
 	reader, writer = yield from asyncio.open_connection(host, 80)
 	header = """GET %s HTTP/1.0\r\nHost: %s\r\n\r\n""" % (url, host)
 	print("header",header)
 	writer.write(header.encode('utf-8')) 
 	# yield from asyncio.sleep(5)    
 	yield from writer.drain()
 	text = yield from reader.read()
 	print(host, url, text.decode())
 	writer.close()
 tasks = [
 	wget('www.cnblogs.com', '/wupeiqi/'),
 	wget('www.cnblogs.com', '/wupeiqi/articles/6229292.html')]
 time1 =time.time()
 loop = asyncio.get_event_loop()  #获取EventLoop
 results = loop.run_until_complete(asyncio.gather(*tasks)) #执行coroutine
 loop.close()
 print(time.time() - time1)
{% endhighlight %}







