---
title: "爬虫基础之网页解析 [CN]"
layout: post
date: 2018-02-24 22:48
tag:
 - Python
 - Web Scraping
 - Tutorial
 - CN
blog: true
author: Topaz
summary: "BeautifulSoup Xpath"
permalink: Spiders-basis-02
---
<h1 class="title"> 爬虫基础之网页解析 </h1>

<h2> Table of Contents </h2>
- [Xpath](#c1)
- [BeautifulSoup](#c2)
<h2 id="c2"> BeautifulSoup </h2>

### 简介
Beautiful Soup 是一个可以从 HTML 或 XML 文件中提取数据的 Python 库。

### 安装
{% highlight python %}
 pip3 install beautifulsoup4
{% endhighlight %}

### 文档读取
{% highlight python %}
 # url 请求获取文档
 import requests
 from bs4 import BeautifulSoup
 response = requests.get("http://www.baidu.com")
 content = response.content
 soup = BeautifulSoup(content, 'html.parser')

 #本地文档
 soup = BeautifulSoup(open('index.html'))
 print(soup)
{% endhighlight %}

### 常用方法
{% highlight raw %}
 #根据 tag 查找
 soup.a 	#返回第一个 a 标签

 #find() & find_all()
 soup.find('div', class_='cat')	# 查找第一个class='cat'的div
 soup.find_all('div', class_='cat') # 查找全部class='cat'的div

 #select 方法
 soup.select('div ul p') #查找 div 下 ul 下的多个 p 标签

 #正则查找
 html_doc = """
 <td>
     <img src="https://morvanzhou.github.io/static/img/course_cover/tf.jpg">
 </td>
 """
 img_links = soup.find_all("img", {"src": re.compile('.*?\.jpg')})	#
 #参考：https://www.crummy.com/software/BeautifulSoup/bs4/doc/index.zh.html#id28
{% endhighlight %}


<h2 id="c1"> Xpath </h2>

### 简介
简单说，xpath就是选择XML文件中节点的方法。所谓节点（node），就是XML文件的最小构成单位，一共分成7种。
{% highlight raw %}
 - element（元素节点）
 - attribute（属性节点）
 - text （文本节点）
 - namespace （名称空间节点）
 - processing-instruction （处理命令节点）
 - comment （注释节点）
 - root （根节点）
{% endhighlight %}

### xpath 表达式规则

1.基本格式
{% highlight raw %}
 - 斜杠（/）作为路径内部的分割符。
 - 同一个节点有绝对路径和相对路径两种写法。
 - 绝对路径（absolute path）必须用"/"起首，后面紧跟根节点，比如/step/step/...。
 - 相对路径（relative path）则是除了绝对路径以外的其他写法，比如 step/step，也就是不使用"/"起首。
 - "."表示当前节点。
 - ".."表示当前节点的父节点
{% endhighlight %}

2.节点选择规则
{% highlight raw %}
 - nodename（节点名称）：表示选择该节点的所有子节点
 - "/"：表示选择根节点
 - "//"：表示选择任意位置的某个节点
 - "@"： 表示选择某个属性
{% endhighlight %}


### 路径表达式及其表达式的结果
{% highlight raw %}
 /bookstore/book[1]			#选取属于 bookstore 子元素的第一个 book 元素
 /bookstore/book[last()]	#选取属于 bookstore 子元素的最后一个 book 元素
 /bookstore/book[last()-1]	#选取属于 bookstore 子元素的倒数第二个 book 元素
 /bookstore/book[position()<3]	#选取最前面的两个属于 bookstore 元素的子元素的 book 元素
 //title[@lang]				#选取所有拥有名为 lang 的属性的 title 元素
 //title[@lang=’eng’]		#选取所有 title 元素，且这些元素拥有值为 eng 的 lang 属性
 /bookstore/book[price>35.00]	#选取 bookstore 元素的所有 book 元素，且其中的 price 元素的值须大于 35.00
 /bookstore/book[price>35.00]/title	#选取 bookstore 元素中的 book 元素的所有 title 元素，且其中的 price 元素的值须大于 35.00
{% endhighlight %}

### Demo
{% highlight python %}
from lxml import etree
html = etree.parse('index.html')
a = html.xpath('//li/a')
print(a[0].text)
{% endhighlight %}
