---
title: "How to copy text from a blocked site using JavaScript"
layout: post
date: 2021-05-02 22:48
tag:
 - JavaScript
 - Fronend
 - EN
blog: true
author: Topaz
summary: "How to Copy and Paste on Sites that Won't Let You"
permalink: shell-scripts-en
---
<h1 class="title"> How to copy text from a blocked site using JavaScript </h1>


<h2> Table of Contents </h2>
- [JavaScript Code](#c1)
- [Usage](#c2)


<h2 id="c1"> JavaScript Code </h2>

{% highlight bash %}
var allowPaste = function(e){
  e.stopImmediatePropagation();
  return true;
};
document.addEventListener('copy', allowPaste, true);
document.addEventListener('paste', allowPaste, true);
{% endhighlight %}

<h2 id="c1"> RUN </h2>

F12 open web developer tool. Run and check if it works well!

<img class="max-wight=800px; left-image" src="/assets/images/posts/build-a-blog/frontend_dev.png" alt="Alt Text">
