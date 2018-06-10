---
title: "Linux 安装 Python3.6"
layout: post
date: 2017-02-24 22:48
tag:
 - Python
 - Installation
blog: true
author: Topaz
summary: "Extra markdown components like highlighted text, side-by-side items, starring/highlighting a blog or project, and embedding gists, videos etc"
permalink: Linux-Python3-Installation
---
<h1 class="title"> Linux 安装 Python3.6  </h1>

### 安装依赖
{% highlight bash %}
yum -y install zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gdbm-devel db4-devel libpcap-devel xz-devel
{% endhighlight %}

### 下载解压缩
{% highlight bash %}
1.官网下载所需 python3 的版本

 wget https://www.python.org/ftp/python/3.6.1/Python-3.6.1.tgz

2.解压

 tar -xf Python-3.6.1.tgz

3.进入目录

 cd Python-3.6.1/
{% endhighlight %}


### 编译安装
{% highlight bash %}
 ./configure --prefix=/usr/local/python3
 make
 make install
{% endhighlight %}


## 创建软链接
{% highlight bash %}
- 移除旧版本
 mv /usr/bin/python /usr/bin/python_bak

- 创建新版本
 ln -s /usr/local/python3/bin/python3 /usr/bin/python
{% endhighlight %}

### 检查版本号
{% highlight bash %}
 python -V
{% endhighlight %}

### yum 和 pip3 问题解决
系统默认版本切换成 py3 后会发生两个问题啦~ 就是 yum 报错和还不能使用 pip3，下面是解决方法

- 解决 yum 报错
{% highlight bash %}
 报错：
 	[root@Topaz tools]# yum -y install xxx
 	File "/usr/bin/yum", line 30
 解决：
	[root@Topaz tools]# vim /usr/bin/yum 
	#!/usr/bin/python2.7		#shangbang指向py2
{% endhighlight %}

- 解决 pip3 不能使用
{% highlight bash %}
 ln -s /usr/local/python3/bin/pip3 /usr/local/bin/pip3  # 加个软链指向 /usr/local/bin/ 下，就能够找到啦
{% endhighlight %}






















