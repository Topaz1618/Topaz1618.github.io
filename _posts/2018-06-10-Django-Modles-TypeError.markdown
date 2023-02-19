---
title: "[CN] Django2.0 models.OneToOneField 报错解决"
layout: post
date: 2018-06-09 22:48
tag:
 - Django
 - Python
 - Troubleshooting
 - CN
blog: true
author: Topaz
summary: "Django 1.8迁移到Django 2.0后models.OneToOneField TypeError的排查步骤"
permalink: Django-Modles-TypeError
---
<h1 class="title"> Use one-to-one relationships in Django2.0 models </h1>

报错信息
{% highlight bash %}
 File "/Users/topaz/Desktop/Project/PWO/models.py", line 110, in UserProfile
 user = models.OneToOneField(User)
 TypeError: __init__() missing 1 required positional argument: 'on_delete'
{% endhighlight %}

解决
{% highlight bash %}
 1.找到报错行
 user = models.OneToOneField(User)

 2.添加 models.CASCADE
 user = models.OneToOneField(User,models.CASCADE)
{% endhighlight %}

原因：
{% highlight raw %}
 之前用的 django1.8 版本，升级后的2.0版本发生变化，需要设置 'on_delete'= models.CASCADE 才能正常使用。
{% endhighlight %}
