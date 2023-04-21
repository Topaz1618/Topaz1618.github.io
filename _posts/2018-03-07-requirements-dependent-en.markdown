---
title: "Automatically generate requirements.txt and use it to install packages"
layout: post
date: 2018-03-07 20:13
tag:
 - Python
 - Installation
 - Deployment
 - EN
blog: true
author: Topaz
summary: "Two ways to generate requirement.txt and use requirement.txt to install dependency packages"
permalink: requirements-dependent-en
---
<h1 class="title"> Automatically generate requirements.txt and use it to install packages </h1>

<h2> Table of Contents </h2>
- [Introduction](#c1)
- [Method 1: Use pip to generate requirements.txt](#c2)
- [Method 2: pipreqs tool to generate requirements.txt](#c3)
- [Use requirements.txt to install packages](#c4)



<h2 id="c1"> Introduction </h2>
 The requirements.txt record all the dependent packages of the current program and their version and is used to rebuild the operating environment dependencies required for the project on another PC.

<h2 id="c2"> Method 1：Use pip to generate requirements.txt </h2>
This method needs to cooperate with Virtualenv, otherwise the packages in the entire environment will be listed.

{% highlight bash %}
pip freeze > requirements.txt
{% endhighlight %}

<h2 id="c3"> Method 2: pipreqs tool to generate requirements.txt</h2>

The advantage of this tool is that it can automatically discover which class libraries are used by scanning the project directory, and automatically generate a dependency list. The disadvantage is that there may be some deviations, which need to be checked and adjusted by yourself.

{% highlight bash %}
 // Install pipreqs
 pip install pipreqs

 // Generate requirements.txt
 pipreqs ./
{% endhighlight %}

<h2 id="c4"> Use requirements.txt to install packages  </h2>
{% highlight bash %}
 pip install -r requirements.txt
{% endhighlight %}
