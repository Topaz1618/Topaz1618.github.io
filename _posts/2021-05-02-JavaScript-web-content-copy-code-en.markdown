---
title: "How to Copy and Paste on Sites that Won't Let You(JavaScript Method)"
layout: post
date: 2017-03-24 22:48
tag:
 - JavaScript
 - Fronend
 - EN
blog: true
author: Topaz
summary: "Copy Text from a Blocked Site using JavaScript Code"
permalink: shell-scripts-en
---
<h1 class="title"> Bash Shell Scripts </h1>


<h2> Table of Contents </h2>
- [JavaScript Code](#c1)

<h2 id="c1"> JavaScript Code </h2>


#### Code's Here!
{% highlight bash %}
var allowPaste = function(e){
  e.stopImmediatePropagation();
  return true;
};
document.addEventListener('copy', allowPaste, true);
document.addEventListener('paste', allowPaste, true);
{% endhighlight %}


#### F12 open web developer tool, copy and run it!

<img src="/assets/images/posts/build-a-blog/frontend_dev.png" alt=""></p>
