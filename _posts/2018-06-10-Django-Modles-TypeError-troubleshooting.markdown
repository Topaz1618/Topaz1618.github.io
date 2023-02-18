---
title: " Django2.0 models.OneToOneField Error troubleshooting [EN]"
layout: post
date: 2018-06-09 22:48
tag:
 - Django
 - TypeError
 - EN
blog: true
author: Topaz
summary: "Extra markdown components like highlighted text, side-by-side items, starring/highlighting a blog or project, and embedding gists, videos etc"
permalink: Django-Modles-TypeError-EN
---
<h1 class="title"> Use one-to-one relationships in Django2.0 models </h1>

Error message
{% highlight bash %}
 File "/Users/topaz/Desktop/Project/PWO/models.py", line 110, in UserProfile
 user = models.OneToOneField(User)
 TypeError: __init__() missing 1 required positional argument: 'on_delete'
{% endhighlight %}

Troubleshooting
{% highlight bash %}
 1.Error throwing line
 user = models.OneToOneField(User)

 2.Add models.CASCADE
 user = models.OneToOneField(User,models.CASCADE)
{% endhighlight %}

Reason:
{% highlight raw %}
 之前用的 django1.8 版本，升级后的2.0版本发生变化，需要设置 'on_delete'= models.CASCADE 才能正常使用。

 The Django1.8 version was used for application before, the upgraded version Django2.0 has changed, need to set 'on_delete'= models.CASCADE.
{% endhighlight %}
