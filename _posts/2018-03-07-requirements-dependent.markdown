---
title: "[CN] 自动生成和安装 requirements.txt 依赖"
layout: post
date: 2018-03-07 19:45
tag:
 - Python
 - Installation
 - Deployment
 - CN
blog: true
author: Topaz
summary: "介绍了 requirements.txt 的生成和使用"
permalink: requirements-dependent
---
<h1 class="title"> 自动生成和安装 requirements.txt 依赖 </h1>

<h2> Table of Contents </h2>
- [Requirements.txt 简介](#c1)
- [方式一： 使用 pip 命令生成](#c2)
- [方式二： pipreqs 工具生成](#c3)
- [安装 requirements.txt 依赖](#c4)



<h2 id="c1"> Requirements.txt 简介 </h2>
 requirements.txt 文件记录了当前程序的所有依赖包及其精确版本号，作用是在另一台PC上重新构建项目所需要的运行环境依赖。

<h2 id="c2"> 方式一：使用 pip 命令生成 </h2>
这个方式需要配合 virtualenv ，否则会列出整个环境中的包

{% highlight bash %}
pip freeze > requirements.txt
{% endhighlight %}

<h2 id="c3"> 方式二：pipreqs 工具生成 </h2>

这个工具的好处是可以通过对项目目录的扫描，自动发现使用了那些类库，自动生成依赖清单，缺点是可能会有些偏差，需要检查并自己调整下。

{% highlight bash %}
- 安装 pipreqs
 pip install pipreqs
- 生成 requirements.txt
 pipreqs ./
{% endhighlight %}

<h2 id="c4"> 安装 requirements.txt 依赖 </h2>
{% highlight bash %}
 pip install -r requirements.txt
{% endhighlight %}
