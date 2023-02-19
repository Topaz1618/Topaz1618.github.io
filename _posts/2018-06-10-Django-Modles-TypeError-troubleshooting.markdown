---
title: "Django2.0 models.OneToOneField TypeError troubleshooting"
layout: post
date: 2018-06-09 22:48
tag:
 - Python
 - Django
 - Troubleshooting
 - EN
blog: true
author: Topaz
summary: "Troubleshooting steps of models.OneToOneField TypeError after migrating Django 1.8 to Django 2.0. "
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
 The Django1.8 version was used for application before, the upgraded version Django2.0 has changed, need to set 'on_delete'= models.CASCADE.
{% endhighlight %}
