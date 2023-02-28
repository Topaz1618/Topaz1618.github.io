---
title: "Converting Docker Compose YAML to Kubernetes YAML"
layout: post
date: 2021-02-26 19:48
tag:
 - Docker
 - DevOps
 - Deployment
 - EN
blog: true
author: Topaz
summary: "This blog post provides a comprehensive guide on how to convert Docker Compose YAML files to Kubernetes YAML files using Kompose. It explains the benefits of using Kubernetes and provides step-by-step instructions for installation Kompose on Mac."
permalink: docker-compose-yml-to-kubernetes-guide
---
<h1 class="title"> Converting Docker Compose YAML to Kubernetes YAML </h1>


<h2> Table of Contents </h2>
- [Introduction to Kompose](#c1)
- [Installing Kompose on Mac](#c2)
- [Understanding Generated Kubernetes YAML](#c3)


<h2 id="c1"> Introduction to Kompose </h2>
    Kompose is a tool that helps to convert docker-compose.yml to Kubernetes YML.

<h2 id="c2"> Installing Kompose on Mac </h2>
{% highlight bash %}
#!/bin/bash
for i in {p2p,shop}
do
	cd /data/$i-tomcat/logs
	/usr/bin/find -name "*.log" -mtime +7|xar rm -f
{% endhighlight %}


<h2 id="c6">SSH batch distribute Script</h2>
1.Download the binary file
{% highlight bash %}
 curl -L https://github.com/kubernetes/kompose/releases/download/v1.22.0/kompose-darwin-amd64 -o kompose
{% endhighlight %}

2.Give the binary executable permissions by running the following command
{% highlight bash %}
 chmod +x kompose
{% endhighlight %}

3.Finally, move the binary to a directory in your system's PATH so that it can be executed from anywhere in the terminal by running the following command:
{% highlight bash %}
 sudo mv ./kompose /usr/local/bin/kompose
{% endhighlight %}


4.Verify that Kompose is installed correctly by running the command
{% highlight bash %}
 kompose version
{% endhighlight %}



<h2 id="c3"> Understanding Generated Kubernetes YAML</h2>

### Generate a Kubernetes YAML file
{% highlight bash %}
 // Generate a Kubernetes YAML file based on your Docker Compose file
 kompose convert -f docker-compose.yml
{% endhighlight %}

### docker-compose.yml Demo

It defines three services: Redis, MySQL, and web, and specifies the necessary configurations such as image, container name, port mapping, network, and environment variables for each service. It also defines a network called hubspot_net and specifies that it is external, which means that Docker Compose will not attempt to create it.

{% highlight bash %}
version: '3' # The version number

services:
  redis:    # Service name
    image: redis   # The Docker image to use for this service
    container_name: hubspot_redis       # Container Name
    restart: always                #  The restart policy for the container
    command: redis-server
    ports:                         # Port mapping. <Your Local Port>:<Container Port>
      - "6379:6379"
    networks:
      - hubspot_net
#    environment:
#      REDIS_PASSWORD: 123456    # Redis Password

  mysql:                                # Service name
    image: mysql:8.0                    # The Docker image to use for this service
    container_name: hotspotDB           # Container Name
    ports:                              # Port mapping. <Your Local Port>:<Container Port>
      - "3307:3306"
    restart: always                        # The restart policy for the container
    networks:                              # Specified the network application use
      - hubspot_net                        # Specified the network name be used
    environment:                           # set env varible
      MYSQL_DATABASE: "hotspotDB"
      MYSQL_ROOT_PASSWORD: "12345678"
      MYSQL_ROOT_HOST: "%"
    command: mysql -hlocalhost -uroot -p123456  # The command to run when the container starts
    volumes:                               # Mapping your host path and container path
      - "/var/lib/mysql/hotspotDB:/var/lib/mysql"   # </path/on/host>:</path/in/container>
      - "/var/lib/mysql/hotspotDB/conf/my.cnf:/etc/my.cnf"
      - "/var/lib/mysql/hotspotDB/init:/docker-entrypoint-initdb.d/" # Placed SQL scripts

  web:
    build: .
    restart: always
    command: python app.py

    ports:
      - "8001:5000"
    volumes:
      - .:/app
#      - .aws:/root/.aws
    depends_on:      # Configure the services that current application depends on
      - redis
      - mysql
    networks:
      - hubspot_net # Specified the network name be used

# Connect your application's containers together so that they can communicate
# With each other using the container names rather than IP addresses.
networks: # Define networks
  hubspot_net:  # Network name
    external:
      name: hubspot_net # If the network already exists, it will be used. If it doesn't exist, Docker Compose will create it.
#    external: true # If the network already exists, it will be used. If it doesn't exist, Docker Compose will not attempt to create it

{% endhighlight %}

### Check the generate result
{% highlight bash %}

 $ ll *.yaml
 -rw-r--r--  1 topaz  staff   316 Feb 28 18:08 hubspot_net-networkpolicy.yaml
 -rw-r--r--  1 topaz  staff   247 Feb 28 18:08 mysql-claim0-persistentvolumeclaim.yaml
 -rw-r--r--  1 topaz  staff   247 Feb 28 18:08 mysql-claim1-persistentvolumeclaim.yaml
 -rw-r--r--  1 topaz  staff   247 Feb 28 18:08 mysql-claim2-persistentvolumeclaim.yaml
 -rw-r--r--  1 topaz  staff  1700 Feb 28 18:08 mysql-deployment.yaml
 -rw-r--r--  1 topaz  staff   372 Feb 28 18:08 mysql-service.yaml
 -rw-r--r--  1 topaz  staff   844 Feb 28 18:08 redis-deployment.yaml
 -rw-r--r--  1 topaz  staff   372 Feb 28 18:08 redis-service.yaml
 -rw-r--r--  1 topaz  staff   243 Feb 28 18:08 web-claim0-persistentvolumeclaim.yaml
 -rw-r--r--  1 topaz  staff  1049 Feb 28 18:08 web-deployment.yaml
 -rw-r--r--  1 topaz  staff   366 Feb 28 18:08 web-service.yaml
{% endhighlight %}

### Introduction of Kubernetes YAML files
All of these YAML files define the desired state of the various components of your application and allow Kubernetes to manage and orchestrate them.

1. hubspot_net-networkpolicy.yaml: Kubernetes NetworkPolicy resource that defines rules for traffic flow to and from pods in the hubspot_net namespace. It specifies which traffic is allowed and which traffic is blocked.
2. mysql-claim0-persistentvolumeclaim.yaml, mysql-claim1-persistentvolumeclaim.yaml, and mysql-claim2-persistentvolumeclaim.yaml: Kubernetes PersistentVolumeClaim resources that define the storage requirements for the MySQL pods. They request storage from the storage provider and provide a mechanism for Kubernetes to dynamically provision the required storage.
3. mysql-deployment.yaml: Kubernetes Deployment resource that defines the desired state for the MySQL pods. It specifies the number of replicas, the container image to use, the ports to expose, and other configuration options.
4. mysql-service.yaml: Kubernetes Service resource that provides a stable IP address and DNS name for the MySQL pods. It allows other pods in the cluster to connect to the MySQL pods using a well-known name and port, regardless of the pod's IP address.
5. redis-deployment.yaml: Kubernetes Deployment resource that defines the desired state for the Redis pod. It specifies the container image to use, the ports to expose, and other configuration options.
6. redis-service.yaml: Kubernetes Service resource that provides a stable IP address and DNS name for the Redis pod. It allows other pods in the cluster to connect to the Redis pod using a well-known name and port, regardless of the pod's IP address.
7. web-claim0-persistentvolumeclaim.yaml: Kubernetes PersistentVolumeClaim resource that defines the storage requirements for the web pod. It requests storage from the storage provider and provides a mechanism for Kubernetes to dynamically provision the required storage.
8. web-deployment.yaml: Kubernetes Deployment resource that defines the desired state for the web pod. It specifies the container image to use, the ports to expose, and other configuration options.
9. web-service.yaml: Kubernetes Service resource that provides a stable IP address and DNS name for the web pod. It allows other pods in the cluster to connect to the web pod using a well-known name and port, regardless of the pod's IP address.
