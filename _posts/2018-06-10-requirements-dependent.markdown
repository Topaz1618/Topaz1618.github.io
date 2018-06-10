---
title: "自动生成和安装 requirements.txt 依赖"
layout: post
date: 2017-02-24 22:48
tag:
 - Project
 - github
blog: true
author: Topaz
summary: "Extra markdown components like highlighted text, side-by-side items, starring/highlighting a blog or project, and embedding gists, videos etc"
permalink: requirements-dependent
---
<h1 class="title"> 自动生成和安装 requirements.txt 依赖 </h1>


### requirements.txt 简介
 requirements.txt 文件记录了当前程序的所有依赖包及其精确版本号，作用是在另一台PC上重新构建项目所需要的运行环境依赖

### 使用 pip 命令自动生成 requirements.txt
这个方式需要配合 virtualenv ，否则会列出整个环境中的包，使用下面的 pipreqs 解决这个问题

{% highlight bash %}
pip freeze > requirements.txt
{% endhighlight %}

### 使用 pipreqs 生成 requirements.txt

这个工具的好处是可以通过对项目目录的扫描，自动发现使用了那些类库，自动生成依赖清单，缺点是可能会有些偏差，需要检查并自己调整下。

- 安装 pipreqs
{% highlight bash %}
 pip install pipreqs
{% endhighlight %}

- 生成 requirements.txt
{% highlight bash %}
 pipreqs ./
{% endhighlight %}

### 安装 requirements.txt 依赖

{% highlight bash %}
 pip install -r requirements.txt
{% endhighlight %}
















