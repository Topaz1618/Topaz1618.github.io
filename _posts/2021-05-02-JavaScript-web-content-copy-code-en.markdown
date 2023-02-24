---
title: "How to copy text from a blocked site using JavaScript"
layout: post
date: 2021-05-02 22:48
tag:
 - JavaScript
 - Frontend
 - EN
blog: true
author: Topaz
summary: "How to Copy and Paste on Sites that Won't Let You"
permalink: shell-scripts-en
---
<h1 class="title"> How to copy text from a blocked site using JavaScript </h1>


<h2 id="c1"> Let's get straight to the point! </h2>

- Step 1: Copy the below code to the web developer tool
- Step 2: Run it!
- Step 3: Done

<h2 id="c1"> Code </h2>

{% highlight bash %}
var allowPaste = function(e){
  e.stopImmediatePropagation();
  return true;
};
document.addEventListener('copy', allowPaste, true);
document.addEventListener('paste', allowPaste, true);
{% endhighlight %}
