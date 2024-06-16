---
title: "Ubuntu Tools"
layout: post
date: 2023-03-26 10:21
tag:
 - macOS
 - CN
blog: true
author: Topaz
summary: "Explore how to use Docker's features and tools such as Swarm, NGINX, and Prometheus to achieve Kubernetes-like reliability, scalability, and availability for your applications."
permalink: mac-tools
---
<h1 class="title"> Ubuntu 配置工具 </h1>


<h2> Table of Contents </h2>

- [1. tmux](#c1)
- [2. Tmux 常用命令](#c2)


<h2 id="c1" > 1. Tmux 切换关键键 </h2>

```
# vim ~/.tmux.conf
unbind C-b
set-option -g prefix C-a
bind-key C-a send-prefix
```

加载
```
tmux source-file ~/.tmux.conf
```

<h2 id="c2"> 2. Tmux 常用命令 </h2>

进入指定窗口
```
tmux a -t:0  # 0 窗口 number
```

新建窗口
```
Ctrl + A，然后松开，再按 c
```

新建窗格
上下
```
Ctrl + A，然后松开，再按 "
```

左右
```
Ctrl + A，然后松开，再按 %
```


向上翻滚
```
按下 Ctrl + A，然后松开，再按 [ 键
```


退出
```
exit
```
