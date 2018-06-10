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

1. 安装依赖
{% highlight raw %}
yum -y install zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gdbm-devel db4-devel libpcap-devel xz-devel
{% endhighlight %}

2. 下载解压缩
- 官网下载所需 python3 的版本
{% highlight raw %}
 wget https://www.python.org/ftp/python/3.6.1/Python-3.6.1.tgz
{% endhighlight %}

- 解压
{% highlight raw%}
 tar -xf Python-3.6.1.tgz
{% endhighlight %}

- 进入目录
{% highlight raw%}
 cd Python-3.6.1/
{% endhighlight %}

3. 编译
{% highlight raw%}
 ./configure --prefix=/usr/local/python3
{% endhighlight %}

4. 安装
{% highlight raw%}
 make
 make install
{% endhighlight %}

5. 创建软链接
- 移除旧版本
{% highlight raw%}
 mv /usr/bin/python /usr/bin/python_bak
{% endhighlight %}

- 创建新版本
{% highlight raw%}
 ln -s /usr/local/python3/bin/python3 /usr/bin/python
{% endhighlight %}

6. 检查版本号
{% highlight raw%}
 python -V
{% endhighlight %}

7. yum 和 pip3 问题解决
系统默认版本切换成 py3 后会发生两个问题啦~ 就是 yum 报错和还不能使用 pip3，下面是解决方法

- 解决 yum 报错
{% highlight raw%}
 报错：
 	[root@Topaz tools]# yum -y install xxx
 	File "/usr/bin/yum", line 30
 解决：
	[root@Topaz tools]# vim /usr/bin/yum 
	#!/usr/bin/python2.7		#shangbang指向py2
{% endhighlight %}

- 解决 pip3 不能使用
{% highlight raw%}
 ln -s /usr/local/python3/bin/pip3 /usr/local/bin/pip3  # 加个软链指向 /usr/local/bin/ 下，就能够找到啦
{% endhighlight %}






















