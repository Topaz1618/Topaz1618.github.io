---
title: "Install and Use Ngrok to Securely Expose Local Web Server to the Internet"
layout: post
date: 2023-02-26 10:10
tag:
 - Installation
 - DevOps
 - Tools
 - EN
blog: true
author: Topaz
summary: "Learn how to easily expose your local web server to the internet using Ngrok, a free, easy-to-use tool that creates a secure public URL for your localhost server."
permalink: ngrok-installation
---
<h1 class="title"> Install and Use Ngrok to Securely Expose Local Web Server to the Internet </h1>


<h2> Table of Contents </h2>

- [1. Introduction to Ngrok](#c1)
- [2. Installing Ngrok](#c2)
- [3. Using Ngrok: Starting a tunnel](#c3)

<h2 id="c1" > 1. Introduction to Ngrok </h2>

Ngrok is a tool that allows you to expose a web server running on your local machine to the internet. It creates a secure tunnel between your local machine and a public URL, which can be used to access your local web server from anywhere in the world. This can be useful for testing and debugging web applications, as well as for providing access to locally hosted services to users outside of your local network.

<h2 id="c2"> 2. Installing Ngrok </h2>

1. Go to the Ngrok website at https://ngrok.com/
2. Click on the "Get started for free" button on the home page.
3. Download the Ngrok executable file
4. Extract the downloaded file to a directory on your computer.


<h2 id="c3"> 3. Using Ngrok: Starting a tunnel </h2>

Ngrok will generate a public URL that you can use to access your server from public internet. (for example: https://<random_str>.ngrok.io)

{% highlight bash %}
// 1. Change the directory to the path where Ngrok is installed
$ cd ~/Downloads/

// 2. Starting a tunnel (./ngrok http <Local Ip>:<Port of Server need to expose>)
$ ./ngrok http 127.0.0.1:8080
 Session Status                online                                                                                                 
 Account                       topaz@gmail.com(Plan:Free)                                                                       
 Update                        update available (version 3.2.2, Ctrl-U to update)                                                     
 Version                       3.1.1                                                                                                  
 Region                        Asia Pacific (ap)                                                                                      
 Latency                       83ms                                                                                                   
 Web Interface                 http://127.0.0.1:4040                                                                                  
 Forwarding                    https://<ramdom_str>.ngrok.io -> http://127.0.0.1:8080

{% endhighlight %}

Try it out! Access the https://<random_str>.ngrok.io on your browser.
