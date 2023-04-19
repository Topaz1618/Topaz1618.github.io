---
title: "Creating Your Own Private Docker Registry"
layout: post
date: 2022-04-02 22:22
tag:
 - Installation
 - DevOps
 - CICD
 - EN
blog: true
author: Topaz
summary: "This blog covers the steps to create a private Docker registry, from choosing a server to pushing and pulling images, with a Docker Compose file."
permalink: docker-registry-installation
---
<h1 class="title"> Creating Your Own Private Docker Registry </h1>


<h2> Table of Contents </h2>

- [0. Prerequisites](#c0)
- [1. Creating a directory to store registry data](#c1)
- [2. Creating a self-signed SSL certificate for the registry](#c2)
- [3. Setting up a Docker Registry Service with Docker Compose](#c3)
- [4. Managing Docker Images in Your Private Registry](#c4)


<h2 id="c0" > 0. Prerequisites </h2>
- Docker installed <a style="color: #AED6F1" href="https://docs.docker.com/get-docker/"> Get Docker </a>
- Docker Compose installed <a style="color: #AED6F1" href="https://docs.docker.com/compose/install/"> Install the Compose plugin </a>

<h2 id="c1" > 1. Creating a directory to store registry data </h2>

{% highlight bash %}
$ mkdir -p /Users/Topaz/opt/docker/registry/data
{% endhighlight %}


<h2 id="c2"> 2. Creating a self-signed SSL certificate for the registry </h2>

{% highlight bash %}
 // Step 1: Createing a directory to store certificate
 $ mkdir -p /Users/Topaz/opt/docker/registry/certs

 // Step 2: Creating a self-signed SSL certificate
  openssl req -newkey rsa:4096 -nodes -sha256 -keyout /Users/Topaz/opt/docker/registry/certs/domain.key -x509 -days 365 -out /Users/Topaz/opt/docker/registry/certs/domain.crt
{% endhighlight %}


<h2 id="c3"> 3. Setting up a Docker Registry Service with Docker Compose </h2>
1.Create a directory to store docker-compose.yml for easy to maintenance
{% highlight bash %}
 $ mkdir -p /Users/Topaz/Projects/DockerRegistry
{% endhighlight %}

2.Create a Docker Compose file to define your registry service.
{% highlight bash %}
 $ cd /Users/Topaz/Projects/DockerRegistry

 $ vim docker-compose.yml
 version: '3'
 services:
   registry:
     image: registry:2
     ports:
       - "5000:5000"
     environment:
       REGISTRY_AUTH: htpasswd
       REGISTRY_AUTH_HTPASSWD_PATH: /auth/htpasswd
       REGISTRY_AUTH_HTPASSWD_REALM: Registry Realm
       REGISTRY_HTTP_TLS_CERTIFICATE: /certs/domain.crt
       REGISTRY_HTTP_TLS_KEY: /certs/domain.key
     volumes:
       - ./htpasswd:/auth/htpasswd
       - ./registry:/var/lib/registry
       - /Users/Topaz/opt/docker/registry/data:/var/lib/registry/data
       - /Users/Topaz/opt/docker/registry/certs:/certs
{% endhighlight %}

3. Start registry service using the following command:
{% highlight bash %}
 $ docker-compose up -d
{% endhighlight %}

<h2 id="c4" > 4. Managing Docker Images in Your Private Registry </h2>

1. Create username and password for your registry
{% highlight bash %}
 // Checking you are under correct path
 $ pwd
 /Users/Topaz/Projects/DockerRegistry

  // Creating directory to store your username and password
 $ mkdir registry

 // Create password
 $ docker run --rm --entrypoint \
     htpasswd httpd:2 -Bbn \
     admin 12345678 > ./registry/htpasswd
{% endhighlight %}

List the images that exist on the your Docker registry
{% highlight bash %}
 $ docker search localhost:5000
{% endhighlight %}

Push
