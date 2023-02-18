---
title: "爬虫基础之 Requests 库 [CN]"
layout: post
date: 2018-02-24 22:48
tag:
 - 爬虫
 - Web Crawler
 - Scraping
 - Spider
 - CN
blog: true
author: Topaz
summary: "Requests Demo"
permalink: Spiders-basis-01
---
<h1 class="title"> 爬虫基础之 Requests 库  </h1>

<h2> Table of Contents </h2>
- [Requests 库介绍](#c1)
- [Requests 库常用方法](#c2)
- [高级用法](#c3)
- [Demo](#c4)
- [参数示例](#c5)

<h2 id="c1"> Requests 库介绍 </h2>
 Requests 库是常用的http请求库，使用 requests 库可以模拟浏览器的请求，比起之前用到的urllib，requests模块的api更加便捷,更加人性化（本质就是封装了urllib3）

<h2 id="c2"> Requests 常用方法介绍 </h2>

### 安装 requests
{% highlight python %}
 pip3 install requests
{% endhighlight %}

### GET请求
{% highlight python %}
 import requests
 response = requests.get("http://httpbin.org/get")	 # 发送请求
 print(response.text)	#获取返回的html信息
{% endhighlight %}

#### POST 请求
{% highlight python %}
 import Requests
 import json
 url = 'https://api.github.com/some/endpoint'
 headers = {'content-type': 'application/json'}
 form_data = {'username': '123456'}
 #携带请求头和数据实例
 ret = requests.post(url, data=json.dumps(form_data),headers=headers)
{% endhighlight %}

### 其它方法
{% highlight python %}
 - requests.get(url, params=None, **kwargs)
 - requests.post(url, data=None, json=None, **kwargs)
 - requests.put(url, data=None, **kwargs)
 - requests.head(url, **kwargs)
 - requests.delete(url, **kwargs)
 - requests.patch(url, data=None, **kwargs)
 - requests.options(url, **kwargs)
 #以上方法均是在此方法的基础上构建
 - requests.request(method, url, **kwargs)
{% endhighlight %}

### 响应内容获取
{% highlight python %}
 response.json()	#内置的 JSON 解码器
 response.headers	#字典形式展示响应头部
 response.status_code #响应状态码
 response.content 	#以字节的方式访问请求响应体
 response.url 		#获取请求的url
 response.cookies   #获取cookie
{% endhighlight %}

### 错误与异常
{% highlight raw %}
 - 网络问题（如：DNS 查询失败、拒绝连接等）：Requests 会抛出一个 ConnectionError 异常
 - HTTP 请求返回不成功的状态码：Response.raise_for_status() 会抛出一个 HTTPError 异常
 - 请求超时：抛出 Timeout 异常
 - 请求超过了设定的最大重定向次数：抛出 TooManyRedirects 异常
 所有Requests显式抛出的异常都继承自 requests.exceptions.RequestException
{% endhighlight %}

<h2 id="c3"> 高级用法 </h2>
基于以上的基础姿势，我们就可以做一些其它的骚操作啦 ~~

#### 上传文件
{% highlight python %}
 files = {'picture': open('catty.png', 'rb')}
 response = requests.post('http://httpbin.org/post', files=files)
{% endhighlight %}

#### 获取cookie
{% highlight python %}
 response = requests.get('https://www.baidu.com')
 for k, v in response.cookies.items():
     print(k,'',v)	# 这里根据需求保存
{% endhighlight %}

#### 保持会话

会话对象让你能够跨请求保持某些参数。它也会在同一个 Session 实例发出的所有请求之间保持 cookie， 期间使用 urllib3 的 connection pooling 功能。所以如果你向同一主机发送多个请求，底层的 TCP 连接将会被重用，从而带来显著的性能提升。
{% highlight python %}
 session = requests.session()
 response = session.get('http://httpbin.org/cookies/set/number/123456')
{% endhighlight %}

#### 保存图片
{% highlight python %}
 response = requests.get('https://www.baidu.com/img/bd_logo1.png', headers=headers)
 with open('baidu.png', 'wb') as f:
 	f.write(response.content)
{% endhighlight %}

{% highlight raw %}
参考：http://docs.python-requests.org/zh_CN/latest/user/advanced.html
{% endhighlight %}

<h2 id="c4"> Demo </h2>

#### 抽屉网点赞
{% highlight python %}
 import requests
 - 方式一
 #首先登陆任何页面，获取cookie
 i1 = requests.get(url="http://dig.chouti.com/help/service")
 i1_cookies = i1.cookies.get_dict()

 #用户登陆，携带上一次的cookie，后台对cookie中的 gpsd 进行授权
 i2 = requests.post(
 	url="http://dig.chouti.com/login",
 	data={
 		'phone': "8613217981270",
 		'password': "123456",
 		'oneMonth': ""
 		},

 	cookies=i1_cookies	)

 #点赞（只需要携带已经被授权的gpsd即可）
 gpsd = i1_cookies['gpsd']
 i3 = requests.post(
 	url="http://dig.chouti.com/link/vote?linksId=14211711",
 	cookies={'gpsd': gpsd})


 - 方式二
 session = requests.Session()
 i1 = session.get(url="http://dig.chouti.com/help/service")
 i2 = session.post(
 	url="http://dig.chouti.com/login",
 	data={
 		'phone': "8613217981270",
 		'password': "123456",
 		'oneMonth': ""})
 i3 = session.post(url="http://dig.chouti.com/link/vote?linksId=14211711")
 print(i3.text)
{% endhighlight %}

<h2 id="c5"> 参数示例 </h2>
{% highlight python %}
 def param_method_url():
 	# requests.request(method='get', url='http://127.0.0.1:8000/test/')
 	# requests.request(method='post', url='http://127.0.0.1:8000/test/')
 	pass
 def param_param():
 	# - 可以是字典
 	# - 可以是字符串
 	# - 可以是字节（ascii编码以内）

 	# requests.request(method='get',
 	# url='http://127.0.0.1:8000/test/',
 	# params={'k1': 'v1', 'k2': '水电费'})

 	# requests.request(method='get',
 	# url='http://127.0.0.1:8000/test/',
 	# params="k1=v1&k2=水电费&k3=v3&k3=vv3")

 	# requests.request(method='get',
 	# url='http://127.0.0.1:8000/test/',
 	# params=bytes("k1=v1&k2=k2&k3=v3&k3=vv3", encoding='utf8'))

 	# 错误
 	# requests.request(method='get',
 	# url='http://127.0.0.1:8000/test/',
 	# params=bytes("k1=v1&k2=水电费&k3=v3&k3=vv3", encoding='utf8'))
 	pass


 def param_data():
 	# 可以是字典
 	# 可以是字符串
 	# 可以是字节
 	# 可以是文件对象

 	# requests.request(method='POST',
 	# url='http://127.0.0.1:8000/test/',
 	# data={'k1': 'v1', 'k2': '水电费'})

 	# requests.request(method='POST',
 	# url='http://127.0.0.1:8000/test/',
 	# data="k1=v1; k2=v2; k3=v3; k3=v4"
 	# )

 	# requests.request(method='POST',
 	# url='http://127.0.0.1:8000/test/',
 	# data="k1=v1;k2=v2;k3=v3;k3=v4",
 	# headers={'Content-Type': 'application/x-www-form-urlencoded'}
 	# )

 	# requests.request(method='POST',
 	# url='http://127.0.0.1:8000/test/',
 	# data=open('data_file.py', mode='r', encoding='utf-8'), # 文件内容是：k1=v1;k2=v2;k3=v3;k3=v4
 	# headers={'Content-Type': 'application/x-www-form-urlencoded'}
 	# )
 	pass


 def param_json():
 	# 将json中对应的数据进行序列化成一个字符串，json.dumps(...)
 	# 然后发送到服务器端的body中，并且Content-Type是 {'Content-Type': 'application/json'}
 	requests.request(method='POST',
 					url='http://127.0.0.1:8000/test/',
 					json={'k1': 'v1', 'k2': '水电费'})


 def param_headers():
 	# 发送请求头到服务器端
 	requests.request(method='POST',
 					url='http://127.0.0.1:8000/test/',
 					json={'k1': 'v1', 'k2': '水电费'},
 					headers={'Content-Type': 'application/x-www-form-urlencoded'}
 					)


 def param_cookies():
 	# 发送Cookie到服务器端
 	requests.request(method='POST',
 					url='http://127.0.0.1:8000/test/',
 					data={'k1': 'v1', 'k2': 'v2'},
 					cookies={'cook1': 'value1'},
 					)
 	# 也可以使用CookieJar（字典形式就是在此基础上封装）
 	from http.cookiejar import CookieJar
 	from http.cookiejar import Cookie

 	obj = CookieJar()
 	obj.set_cookie(Cookie(version=0, name='c1', value='v1', port=None, domain='', path='/', secure=False, expires=None,
 						discard=True, comment=None, comment_url=None, rest={'HttpOnly': None}, rfc2109=False,
 						port_specified=False, domain_specified=False, domain_initial_dot=False, path_specified=False)
 				)
 	requests.request(method='POST',
 					url='http://127.0.0.1:8000/test/',
 					data={'k1': 'v1', 'k2': 'v2'},
 					cookies=obj)


 def param_files():
 	# 发送文件
 	# file_dict = {
 	# 'f1': open('readme', 'rb')
 	# }
 	# requests.request(method='POST',
 	# url='http://127.0.0.1:8000/test/',
 	# files=file_dict)

 	# 发送文件，定制文件名
 	# file_dict = {
 	# 'f1': ('test.txt', open('readme', 'rb'))
 	# }
 	# requests.request(method='POST',
 	# url='http://127.0.0.1:8000/test/',
 	# files=file_dict)

 	# 发送文件，定制文件名
 	# file_dict = {
 	# 'f1': ('test.txt', "hahsfaksfa9kasdjflaksdjf")
 	# }
 	# requests.request(method='POST',
 	# url='http://127.0.0.1:8000/test/',
 	# files=file_dict)

 	# 发送文件，定制文件名
 	# file_dict = {
 	#     'f1': ('test.txt', "hahsfaksfa9kasdjflaksdjf", 'application/text', {'k1': '0'})
 	# }
 	# requests.request(method='POST',
 	#                  url='http://127.0.0.1:8000/test/',
 	#                  files=file_dict)

 	pass


 def param_auth():
 	from requests.auth import HTTPBasicAuth, HTTPDigestAuth

 	ret = requests.get('https://api.github.com/user', auth=HTTPBasicAuth('wupeiqi', 'sdfasdfasdf'))
 	print(ret.text)

 	# ret = requests.get('http://192.168.1.1',
 	# auth=HTTPBasicAuth('admin', 'admin'))
 	# ret.encoding = 'gbk'
 	# print(ret.text)

 	# ret = requests.get('http://httpbin.org/digest-auth/auth/user/pass', auth=HTTPDigestAuth('user', 'pass'))
 	# print(ret)
 	#


 def param_timeout():
 	# ret = requests.get('http://google.com/', timeout=1)
 	# print(ret)

 	# ret = requests.get('http://google.com/', timeout=(5, 1))
 	# print(ret)
 	pass


 def param_allow_redirects():
 	ret = requests.get('http://127.0.0.1:8000/test/', allow_redirects=False)
 	print(ret.text)


 def param_proxies():
 	# proxies = {
 	# "http": "61.172.249.96:80",
 	# "https": "http://61.185.219.126:3128",
 	# }

 	# proxies = {'http://10.20.1.128': 'http://10.10.1.10:5323'}

 	# ret = requests.get("http://www.proxy360.cn/Proxy", proxies=proxies)
 	# print(ret.headers)


 	# from requests.auth import HTTPProxyAuth
 	#
 	# proxyDict = {
 	# 'http': '77.75.105.165',
 	# 'https': '77.75.105.165'
 	# }
 	# auth = HTTPProxyAuth('username', 'mypassword')
 	#
 	# r = requests.get("http://www.google.com", proxies=proxyDict, auth=auth)
 	# print(r.text)

 	pass

 def param_stream():
 	ret = requests.get('http://127.0.0.1:8000/test/', stream=True)
 	print(ret.content)
 	ret.close()

 	# from contextlib import closing
 	# with closing(requests.get('http://httpbin.org/get', stream=True)) as r:
 	# # 在此处理响应。
 	# for i in r.iter_content():
 	# print(i)

 def requests_session():
 	import requests

 	session = requests.Session()

 	### 1、首先登陆任何页面，获取cookie

 	i1 = session.get(url="http://dig.chouti.com/help/service")

 	### 2、用户登陆，携带上一次的cookie，后台对cookie中的 gpsd 进行授权
 	i2 = session.post(
 		url="http://dig.chouti.com/login",
 		data={
 			'phone': "8615131255089",
 			'password': "xxxxxx",
 			'oneMonth': ""
 		}
 	)

 	i3 = session.post(
 		url="http://dig.chouti.com/link/vote?linksId=8589623",
 	)
 	print(i3.text)
{% endhighlight %}
