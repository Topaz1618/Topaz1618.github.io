---
title: "Installing Python 3.6 on Linux"
layout: post
date: 2018-02-24 21:12
tag:
 - Python
 - Linux
 - Installation
 - CN
blog: true
author: Topaz
summary: "How to install Python 3.6 on Linux(Build from source code)"
permalink: Linux-Python3-Installation-en
---
<h1 class="title"> Installing Python 3.6 on Linux  </h1>

<h2> Table of Contents </h2>
- [Python3.6 Installation](#c1)
- [Problem Solving](#c2)


<h2 id="c1"> Python3.6 Installation </h2>

#### Install Packages
{% highlight bash %}
yum -y install zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gdbm-devel db4-devel libpcap-devel xz-devel
{% endhighlight %}

#### Download and unpack
{% highlight bash %}
1. Download specific python 3 version as needed
 wget https://www.python.org/ftp/python/3.6.1/Python-3.6.1.tgz

2.Unpack
 tar -xf Python-3.6.1.tgz

3.进入目录
 cd Python-3.6.1/
{% endhighlight %}

#### Configure
{% highlight bash %}
 ./configure --prefix=/usr/local/python3
 make
 make install
{% endhighlight %}


#### Create symbolic link
{% highlight bash %}
1.Remove the symbolic link of old version Python
 mv /usr/bin/python /usr/bin/python_bak

2.Symlink /usr/local/python3/bin/python3 to /usr/bin/python
 ln -s /usr/local/python3/bin/python3 /usr/bin/python
{% endhighlight %}

#### Check version
{% highlight bash %}
 python -V
{% endhighlight %}


<h2 id="c2"> Problem Solving </h2>
After switching the default Python version of the system to Python 3, There have two problems will occur~ yum and pip3 cannot be used, the solution is as follows

#### How to fix yum Error
{% highlight bash %}
 Error：
 	[root@Topaz tools]# yum -y install xxx
 	File "/usr/bin/yum", line 30
 Fixing：
 	//shangbang point to python 2.7
	[root@Topaz tools]# vim /usr/bin/yum
	#!/usr/bin/python2.7		

	// CentOS7 need to modify /usr/libexec/urlgrabber-ext-down as well
	[root@Topaz tools]# vim /usr/libexec/urlgrabber-ext-down
	#!/usr/bin/python2.7		
{% endhighlight %}

#### How to fix pip3 can not be used
Symlink /usr/local/python3/bin/pip3 to /usr/bin
{% highlight bash %}
 ln -s /usr/local/python3/bin/pip3 /usr/local/bin/pip3  
{% endhighlight %}
