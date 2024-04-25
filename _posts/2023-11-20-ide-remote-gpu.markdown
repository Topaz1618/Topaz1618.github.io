---
title: "[CN] 本地使用远程 GPU 配置"
layout: post
date: 2023-11-20 21:34
tag:
 - Machine Learning
 - Deep Learning
 - DevOps
 - GPU
 - Python
 - CN
blog: true
author: Topaz
summary: "在本地 Pycharm 和 Jupter 中使用远程 GPU"
permalink: remote-gpu
---
<h1 class="title">  本地使用远程 GPU 配置 </h1>


<h2> Table of Contents</h2>
- [Pycharm](#c1)
- [Jupter](#c2)

<h2 id="c1"> Pycharm 配置使用远程 GPU </h2>

1.  配置SSH连接
{% highlight markdown %}
1.1 创建SSH配置： 在 PyCharm 中打开项目，然后转到 "Tools" -> "Deployment" -> "Configuration"。添加一个新的 SSH 配置，填写远程服务器的相关信息（IP地址、用户名、密码/密钥）。

1.2 测试SSH连接： 确保SSH连接设置正确，并且能够成功连接到远程服务器。
{% endhighlight %}

2. 配置远程 Python 解释器：
{% highlight markdown %}
2.1 打开项目设置： 转到 "Python Interpreter"。

2.2 添加远程解释器： 点击右上角的齿轮图标，选择 "Add..." -> "SSH Interpreter"。选择之前配置好的SSH连接，并输入远程服务器上的 Python 解释器路径（通常在 /usr/bin/python 或类似目录下）。
{% endhighlight %}


3. 创建远程运行配置：
{% highlight markdown %}
3.1 打开运行/调试配置： 转到 "Run" -> "Edit Configurations..."。

3.2 添加新配置： 点击加号 (+) 添加一个新的 Python 配置，选择 "Python"。

3.3 配置远程解释器： 在配置中的 "Python interpreter" 选项中选择之前配置的远程解释器。

3.4 配置运行脚本： 在 "Script path" 中指定您想要在远程服务器上运行的 Python 脚本路径，并填写其他参数。
{% endhighlight %}

4. 配置同步

设置同步目录
{% highlight markdown %}
转到 "Tools" -> "Deployment" -> "Configuration" -> "Mapping"，确保此选项配置。
{% endhighlight %}


设置自动同步
{% highlight markdown %}
4.1 启用自动上传：转到 "Tools" -> "Deployment" -> "Automatic Upload"，确保此选项已启用。

4.2 启用自动下载： 在 "Tools" -> "Deployment" -> "Options" -> "Automatic Download" 中启用自动下载。
{% endhighlight %}

手动同步
{% highlight markdown %}
"Tools" -> "Deployment" 点击 "Upload to ..." 或 "Download from ..." 按钮（取决于您想要的同步方向），然后选择相应的远程目录
{% endhighlight %}


<h2 id="c2"> Jupter 配置使用远程 GPU  </h2>
{% highlight python %}
    pass
{% endhighlight %}
