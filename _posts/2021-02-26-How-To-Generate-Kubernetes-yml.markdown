---
title: "Converting Docker Compose YAML to Kubernetes YAML"
layout: post
date: 2022-02-26 09:48
tag:
 - Docker
 - DevOps
 - Kubernetes
 - Deployment
 - EN
blog: true
author: Topaz
summary: "This blog post provides a comprehensive guide on how to convert Docker Compose YAML files to Kubernetes YAML files using Kompose. It explains the benefits of using Kubernetes and provides step-by-step instructions for installation Kompose on Mac."
permalink: docker-compose-yml-to-kubernetes-guide
---
<h1 class="title"> Converting Docker Compose YAML to Kubernetes YAML </h1>


<h2> Table of Contents </h2>


- [1. Introduction to Kompose](#c1)
    - [1.1 What is Kompose?](#c11)
    - [1.2 Installing Kompose on Mac](#c12)
    - [1.3 Converting Docker Compose YAML to Kubernetes YAML](#c13)
- [2. Docker Compose Demo for Converting to Kubernetes](#c2)
- [3. Understanding Generated Kubernetes YAML](#c3)
    - [3.1 Viewing all generated Kubernetes YAML files](#c31)
    - [3.2 Deployment YAML](#c32)
    - [3.3 Service YAML](#c33)
    - [3.4 PersistentVolumeClaim (PVC) YAML](#c34)
    - [3.5 NetworkPolicy YAML](#c35)      

<h2 id="c1" > 1. Introduction to Kompose </h2>

<h3 id="c11"> 1.1 What is Kompose? </h3>

Kompose is a tool that helps to convert docker-compose.yml to Kubernetes YML.

<h3 id="c12"> 1.2 Installing Kompose on Mac </h3>

{% highlight bash %}
 // Download the binary file
 curl -L https://github.com/kubernetes/kompose/releases/download/v1.22.0/kompose-darwin-amd64 -o kompose

 // Give the binary executable permissions by running the following command
 chmod +x kompose

 // Finally, move the binary to a directory in your system's PATH.
 //So that it can be executed from anywhere in the terminal by running the following command:
 sudo mv ./kompose /usr/local/bin/kompose


 // Verify that Kompose is installed correctly by running the command
 kompose version
{% endhighlight %}

<h3 id="c13"> 1.3 Converting Docker Compose YAML to Kubernetes YAML </h3>

Generate a Kubernetes YAML file based on your Docker Compose file
{% highlight bash %}
 kompose convert -f docker-compose.yml
{% endhighlight %}

<h2 id="c2"> Docker Compose Demo for Converting to Kubernetes</h2>


It defines three services: Redis, MySQL, and Web applications, and specifies the necessary configurations such as image, container name, port mapping, network, and environment variables for each service. It also defines a network called hubspot_net and specifies that it is external, which means that Docker Compose will not attempt to create it.

{% highlight markdown %}
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
 #   external: true # If the network already exists, it will be used. If it doesn't exist, Docker Compose will not attempt to create it
{% endhighlight %}

<h2 id="c3"> Understanding Generated Kubernetes YAML</h2>

<h3 id="c31"> 3.1 Viewing all generated Kubernetes YAML files </h3>


{% highlight bash %}
 $ ll *.yaml
 -rw-r--r--   316 Feb 28 18:08 hubspot_net-networkpolicy.yaml
 -rw-r--r--   247 Feb 28 18:08 mysql-claim0-persistentvolumeclaim.yaml
 -rw-r--r--   247 Feb 28 18:08 mysql-claim1-persistentvolumeclaim.yaml
 -rw-r--r--   247 Feb 28 18:08 mysql-claim2-persistentvolumeclaim.yaml
 -rw-r--r--  1700 Feb 28 18:08 mysql-deployment.yaml
 -rw-r--r--   372 Feb 28 18:08 mysql-service.yaml
 -rw-r--r--   844 Feb 28 18:08 redis-deployment.yaml
 -rw-r--r--   372 Feb 28 18:08 redis-service.yaml
 -rw-r--r--   243 Feb 28 18:08 web-claim0-persistentvolumeclaim.yaml
 -rw-r--r--  1049 Feb 28 18:08 web-deployment.yaml
 -rw-r--r--   366 Feb 28 18:08 web-service.yaml
{% endhighlight %}

The above YAML files were generated using Kompose from the Docker Compose file we provided. By looking at the file names, we can see that the following types of Kubernetes YAML files were generated:

- Deployment YAML
- Service YAML
- PersistentVolumeClaim (PVC) YAML
- NetworkPolicy YAML

In the next sections, we will introduce each of these types in detail and explain their role in Kubernetes.

<h3 id="c32"> 3.2 Deployment YAML </h3>

Deployment YAML is a type of Kubernetes YAML file used to manage the deployment of applications and services in a Kubernetes cluster. It describes the desired state of a deployment, including the number of replicas, the container image to use,  the ports to expose, and other configuration details.

With a Deployment YAML file, you can easily manage and scale your application deployments, update the version of your application, roll back to previous versions, and manage load balancing across replicas.

A typical Deployment YAML file includes a metadata section that defines the name, labels, and annotations for the deployment, as well as a spec section that defines the desired state of the deployment, including the container image, the number of replicas, and other configuration details.

The following Deployment YAML files were generated:
- mysql-deployment.yaml
- web-deployment.yaml
- redis-deployment.yaml


<h3 id="c33"> 3.3 Service YAML </h3>
Service YAML is a type of Kubernetes YAML file used to define a logical set of pods and enable network communication between them. Services provide a stable IP address and DNS name for a set of pods, It allows other pods in the cluster to connect to the pod using a well-known name and port, regardless of the pod's IP address.

With a Service YAML file, you can easily manage and expose your application services to the network, balance traffic between pods, and define service-level access policies.

A typical Service YAML file includes a metadata section that defines the name, labels, and annotations for the service, as well as a spec section that defines the desired state of the service, including the type of service, the selector for the set of pods to expose, and other configuration details.

The following Service YAML files were generated:
- mysql-service.yaml
- web-service.yaml
- redis-service.yaml

<h3 id="c34"> 3.4 PersistentVolumeClaim (PVC) YAML </h3>
PersistentVolumeClaim (PVC) YAML files define the storage requirements for the pods in Kubernetes. They request storage from the storage provider and provide a mechanism for Kubernetes to dynamically provision the required storage.

With a PVC YAML file, you can easily request storage space for your application, specify the access mode (such as read-write or read-only), and define other configuration details.

A typical PVC YAML file includes a metadata section that defines the name, labels, and annotations for the PVC, as well as a spec section that defines the desired state of the PVC, including the storage class, access mode, and storage capacity.

The following PVC YAML files were generated:
- mysql-claim0-persistentvolumeclaim.yaml
- mysql-claim1-persistentvolumeclaim.yaml
- mysql-claim2-persistentvolumeclaim.yaml
- web-claim0-persistentvolumeclaim.yaml


<h3 id="c35"> 3.5 NetworkPolicy YAML </h3>
NetworkPolicy is a type of Kubernetes YAML file used to define rules for network traffic within a Kubernetes cluster. It provides a way to enforce fine-grained network access controls, such as allowing or denying traffic to specific pods based on IP address, port number, or other criteria.

With a NetworkPolicy YAML file, you can easily manage and secure the network traffic of your application, implement segmentation between different components of your application, and prevent unauthorized access to sensitive data.

A typical NetworkPolicy YAML file includes a metadata section that defines the name, labels, and annotations for the network policy, as well as a spec section that defines the desired state of the network policy, including the pod selector, ingress and egress rules, and other configuration details.

The following PVC YAML file were generated:
- hubspot_net-networkpolicy.yaml
