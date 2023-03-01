---
title: "Mastering Kubernetes YAML: A Complete Guide to Writing and Generating YAML Files for Your Applications"
layout: post
date: 2021-02-26 19:48
tag:
 - Docker Compose
 - Kubernetes
 - DevOps
 - Deployment
 - EN
blog: true
author: Topaz
summary: "This blog provides an introduction to Kubernetes YAML files, covering the basics of YAML syntax and the structure of Kubernetes YAML files."
permalink: kubernetes-yml-guide
---
<h1 class="title"> Mastering Kubernetes YAML: A Complete Guide to Writing and Generating YAML Files for Your Applications </h1>


<h2> Table of Contents </h2>

- [1. Introduction](#c1)   
    - [1.1 What are Kubernetes YAML files?](#c1)
    - [1.2 Why use Kubernetes YAML files?](#c1)
- [2. Exploring Kubernetes YAML Syntax, Demo and Applications](#c2)
    - [2.1 Kubernetes YAML Syntax](#c21)
    - [2.2 Kubernetes YAML Demo](#c22)
    - [2.3 Kubernetes YAML Applications](#c23)
- [3. Common Types of Kubernetes YAML Files](#c3)
    - [3.1 Deployment YAML](#c31)
    - [3.2 Service YAML](#c32)
    - [3.3 Deployment YAML](#c33)
    - [3.4 ConfigMap YAML](#c34)
    - [3.5 Deployment YAML](#c35)
    - [3.6 Secret YAML](#c36)
    - [3.7 PersistentVolumeClaim (PVC) YAML](#c37)
    - [3.8 NetworkPolicy YAML](#c38)    
- [4. Automating Kubernetes YAML Generation](#c4)
    - [4.1 Kompose](#c41)
    - [4.2 Helm](#c42)
    - [4.3 Kustomize](#c43)
    - [Skaffold](#c44)      

<h2 id="c1"> 1. Introduction </h2>

Kubernetes YAML files are an essential component of working with Kubernetes that used to define Kubernetes resources, such as pods, services, deployments, and more, in a declarative manner.

Using YAML files for your Kubernetes resources can offer a range of advantages, including:

- Declarative configuration: YAML files allow you to specify the desired state of your Kubernetes resources, rather than the steps needed to achieve that state.

-  Version control: YAML files can be stored in version control systems, allowing you to track changes to your Kubernetes resources over time.

-  Automation: YAML files can be used with Kubernetes command-line tools or APIs to automate the deployment and management of your Kubernetes resources.

<h2 id="c2"> 2. Kubernetes YAML Syntax, Examples, and Applications </h2>

<h3 id="c21"> 2.1 Kubernetes YAML Syntax </h2>

Common keywords used in Kubernetes YAML files and their explanations:

- apiVersion: The version of the Kubernetes API that the YAML file is written against.

- kind: The type of Kubernetes resource that is being defined in the YAML file (e.g. Deployment, Service, Pod, etc.).
- metadata: The section of the YAML file that contains metadata about the resource, such as the name, labels, and annotations.
- spec: The section of the YAML file that contains the specification for the resource, including details such as the container image, ports, and volumes.
- selector: Used in Service and Deployment YAML files to specify which Pods the Service or Deployment should target.
- containers: The section of a Pod or Deployment YAML file that contains information about the containers that should be run, including the container image, ports, and environment variables.
- ports: The section of a Service or Pod YAML file that specifies which ports should be exposed and how they should be mapped.
- volumeMounts: Used in container specification sections to mount a volume inside the container.
- volumes: The section of a Pod or Deployment YAML file that specifies which volumes should be mounted inside the container.
- replicas: The number of replicas that should be created for a Deployment.
- labels: Used to attach key-value pairs to resources like Pods, Deployments, or Services. Labels can be used to filter resources by selectors.
-  annotations: Used to attach non-identifying metadata to resources, such as descriptions or version numbers.
- imagePullPolicy: Specifies the policy to be used when pulling the container image, such as Always, IfNotPresent, or Never.

<h3 id="c22"> 2.2 Kubernetes YAML Demo </h2>

{% highlight bash %}
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx
          image: nginx
          ports:
            - containerPort: 80
{% endhighlight %}

<h3 id="c23"> 2.3 Kubernetes YAML Application </h2>

For example, to start an application written in Python, depending on Redis and MySQL, you would typically need to create Kubernetes YAML files for the following resources:

- Deployment and Service for Redis
- Deployment and Service for MySQL
- Deployment and Service for your Python application
- Optionally, PersistentVolumeClaim for MySQL and your Python application

Once you have created all of the required YAML files, you can use the kubectl command to create each component in turn. For example, to create the Redis deployment and service, you would run:
{% highlight bash %}
 kubectl apply -f redis-deployment.yaml
 kubectl apply -f redis-service.yaml
{% endhighlight %}

Similarly, to create the MySQL deployment and service, you would run:
{% highlight bash %}
 kubectl apply -f mysql-deployment.yaml
 kubectl apply -f mysql-service.yaml
{% endhighlight %}

And finally, to create the deployment and service for your application, you would run:
{% highlight bash %}
 kubectl apply -f web-deployment.yaml
 kubectl apply -f web-service.yaml
{% endhighlight %}


<h2 id="c4"> 4. Common Types of Kubernetes YAML Files </h2>

- Deployment YAML
- Service YAML
- ConfigMap YAML
- Secret YAML
- PersistentVolumeClaim (PVC) YAML
- NetworkPolicy YAML

<h3 id="c31"> 3.1 Deployment YAML </h2>

This file defines the deployment of your service and specifies the Docker image to use, the number of replicas to run, and the ports to expose. You can also specify resource requirements, liveness and readiness probes, and other configurations in this file.

<h3 id="c32"> 3.2 Service YAML </h2>

This file defines a service for your deployment and specifies the type of service (ClusterIP, NodePort, LoadBalancer), the ports to expose, and the selector to use to match the pods.

<h3 id="c33"> 3.3 ConfigMap YAML (Optional)</h2>

This file defines a ConfigMap object that holds configuration data in key-value pairs. You can use this file to separate the configuration data from the deployment and service YAML files.

<h3 id="c34"> 3.4 Secret YAML (Optional)</h2>

This file defines a Secret object that holds sensitive data such as passwords, access keys, and other credentials. You can use this file to separate the sensitive data from the deployment and service YAML files.

<h3 id="c35"> 3.5 PersistentVolumeClaim(PVC) YAML (Optional)</h2>

This file is a Kubernetes resource used to request storage resources in a cluster. PVCs allow you to abstract the details of the storage away from your deployment and statefulset configurations. A PVC is essentially a request for storage, with the desired characteristics such as access mode, storage class, and size.
 {% highlight bash %}
 apiVersion: v1
 kind: PersistentVolumeClaim
 metadata:
   name: my-pvc
 spec:
   accessModes:
   - ReadWriteOnce
   resources:
     requests:
       storage: 1Gi
 {% endhighlight %}

<h3 id="c36"> 3.6 NetworkPolicy YAML (Optional)</h2>

This file is a Kubernetes resource used to control network traffic in a cluster. Network policies allow you to specify how pods are allowed to communicate with each other and with other network endpoints. You can use network policies to block traffic or allow traffic only between certain pods or namespaces.
{% highlight bash %}
 apiVersion: networking.k8s.io/v1
 kind: NetworkPolicy
 metadata:
   name: deny-all
 spec:
   podSelector: {}
   policyTypes:
   - Ingress
   ingress: []
{% endhighlight %}

<h2 id="c3"> 5. Automating Kubernetes YAML Generation </h2>

You can automate the process of creating and updating your Kubernetes objects by using below Kubernetes configuration management tools:

- Kompose
- Helm
- Kustomize
- Skaffold

<h3 id="c41"> 4.1 Kompose</h2>

 Kompose is a tool that can convert Docker Compose files to Kubernetes YAML files. This can save time and effort when migrating existing Docker-based applications to Kubernetes.  

[Check Here ☞ <a href="https://topaz1618.github.io/docker-compose-yml-to-kubernetes-guide">Converting Docker Compose YAML to Kubernetes YAML</a>]


<h3 id="c42"> 4.2 Helm</h2>

Helm is a package manager for Kubernetes that allows you to define, install, and upgrade applications using YAML files. It uses templates and variables to generate Kubernetes YAML files and can be used to manage complex deployments

<h3 id="c43"> 4.3 Kustomize</h2>

Kustomize is a tool that enables you to customize Kubernetes YAML files without modifying the original files. It allows you to manage multiple environments and generate different YAML files for each environment.
.

<h3 id="c44"> 4.4 Skaffold</h2>

Skaffold is a tool that automates the development workflow for Kubernetes applications. It can automatically generate Kubernetes YAML files and deploy them to a local or remote Kubernetes cluster.
