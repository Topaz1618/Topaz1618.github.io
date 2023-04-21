---
title: "Ensuring Your Website Permanence: Techniques for Making Your Website Run Until the End of Time 😉"
layout: post
date: 2022-04-20 18:59
tag:
 - Flask
 - Web Framework
 - Development
 - Python
blog: true
author: Topaz
summary: "Introduce some random thing of Flask"
permalink: mac-tools
---
<h1 class="title"> Flask Development Tips </h1>



<h2> Table of Contents </h2>
- [1. Different levels for improving website performance](#c0)
- [2. Bottleneck Detection](#c1)
- [1. Code Techniques Level](#c1)
- [2. Web Framework Level](#c2)
- [3. Architecture Level](#c3)


<h2 id="c1"> 1. Different levels for improving website performance </h2>
- Infrastructure Level: This level includes the underlying infrastructure that your website runs on, such as servers, network, and storage. Techniques at this level might include load balancing, auto-scaling, and content delivery networks (CDNs).

- Code Level: This level includes the code that your website is built on, such as HTML, CSS, and JavaScript. Techniques at this level might include code optimization, minimizing HTTP requests, and using asynchronous JavaScript.

- Framework Level: This level includes the web framework that your website is built with, such as Django, Ruby on Rails, or Node.js. Techniques at this level might include caching, database optimization, and using a content management system (CMS).

- Architecture Level: This level includes the overall architecture of your website, such as the design patterns used, the data model, and the application flow. Techniques at this level might include using a microservices architecture, implementing a service-oriented architecture (SOA), and optimizing database schema.


<h2 id="c2"> 1. Bottleneck Detection </h2>

Here are some other common bottlenecks that can affect cloud server performance:
- CPU and Memory Bottlenecks: CPU and memory resources can become a bottleneck when they are not sufficient to handle the workload demands of an application or service. This can cause slow response times, application crashes, or other performance issues.

- Disk I/O Bottlenecks: Disk I/O performance can become a bottleneck when the disk subsystem is not fast enough to handle the data transfer demands of an application or service. This can cause slow read and write speeds, application crashes, or other performance issues.

- Network Congestion: Network congestion can occur when there is too much traffic on the network, causing packets to be dropped or delayed. This can cause slow download and upload speeds, packet loss, and other performance issues.

- Server Load: Server load can become a bottleneck when the server is not powerful enough to handle the workload demands of an application or service. This can cause slow response times, application crashes, or other performance issues.

- Application Design: Application design can become a bottleneck when it is not optimized for performance, scalability, or resource utilization. This can cause slow response times, application crashes, or other performance issues.

- Database Bottlenecks: Database performance can become a bottleneck when the database subsystem is not fast enough to handle the data transfer demands of an application or service. This can cause slow database queries, slow application response times, or other performance issues.

### CPU and Memory Bottlenecks Detection
For example, we have a web application running on an AWS t2.micro instance with 1 vCPU and 1 GB of memory. In general, we check CPU and Memory Bottlenecks Detection by maximum CPU and memory utilization

vCPU: 1
Memory: 1 GB
CPU Utilization: Percentage of time CPU is busy
Memory Utilization = Percentage of memory used

Check the percentage of time the CPU is busy using the "top" command. It should look something like this:
```
%Cpu(s):  1.0 us,  1.0 sy,  0.0 ni, 98.0 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
```
The "%Cpu(s)" line displays the CPU usage information as a breakdown of different types of CPU usage.
- "us": Represent the percentage of time the CPU is spent on user space processes
- "sy": Represent the percentage of time the CPU is spent on system space processes. - "id": Represent the percentage of time the CPU is idle.

Formula: Percentage of time CPU is busy = 100 - "id" value

To check the percentage of time the CPU is busy, using above Formula
```
Percentage of time the CPU is busy = 100% - 98.0% = 2.0%
```

Check the percentage of memory used using the commands "free "or "free -m"(Memory usage information in megabytes (MB)). It should look something like this:
```
total       used       free     shared    buffers     cached
Mem:       8175876    3068764    5107112      42804     128820    1309372
-/+ buffers/cache:    1621572    6554304
Swap:      4194300          0    4194300

```
- "used": Represents the amount of memory currently in use
- "free": Represents the amount of memory available for use

Formula: Percentage of memory used = ("used" / "total") * 100

To check the percentage of time the memory used, using above Formula
```
Percentage of memory used = (3068764 / 8175876) * 100 = 37.5%
```

Generally speaking, if the CPU utilization or Memory Usage consistently exceeds 80-90% over an extended period of time, it could indicate a potential bottleneck.


### Disk I/O Bottlenecks Detection
For example, we have a database running on an AWS EC2 instance with with a high volume of read and write operations. In general, the maximum Disk I/O throughput you can achieve is dependent on the Disk I/O capacity and the Workload demand of the application.

Env
t2.micro launched with an 8 GB General Purpose SSD (gp2) EBS volume




Three main areas check for Disk I/O bottlenecks
- Disk I/O Utilization
- Disk I/O Throughput
- Workload

Check Disk I/O Utilization and Disk I/O Throughput under Linux using iostat
```
$ sudo apt-get install sysstat -y

$ iostat -d -k -x
Linux 5.15.0-56-generic (VM-0-6-ubuntu) 	04/22/23 	_x86_64_	(2 CPU)

Device            r/s     rkB/s   rrqm/s  %rrqm r_await rareq-sz     w/s     wkB/s   wrqm/s  %wrqm w_await wareq-sz     d/s     dkB/s   drqm/s  %drqm d_await dareq-sz     f/s f_await  aqu-sz  %util
loop0            0.78      6.31     0.00   0.00    0.16     8.07    0.00      0.00     0.00   0.00    0.00     0.00    0.00      0.00     0.00   0.00    0.00     0.00    0.00    0.00    0.00   0.03
loop4            0.41     14.63     0.00   0.00    0.06    35.74    0.00      0.00     0.00   0.00    0.00     0.00    0.00      0.00     0.00   0.00    0.00     0.00    0.00    0.00    0.00   0.13
```

The iostat command can provide Disk I/O statistics for each device attached to the system. In this example, we do have two devices(loop0 and loop4) attached to system, use lsof commands to identify which devices are associated with specific workloads or applications.
```
$ sudo lsof /dev/loop2
COMMAND PID USER  FD   TYPE DEVICE SIZE/OFF NODE NAME
snapd   804 root txt    REG    7,4 32485984  150 /snap/snapd/17883/usr/lib/snapd/snapd
```

Let's say /snap/snapd/17883/usr/lib/snapd/snapd is our appliction that need to check bottleneck, and you can see snapd process has a file descriptor (FD) open on the /dev/loop2 device, /dev/loop2 using by it😊. now go back to check value on device loop4. Let's say check %util. If the %util value for a device is consistently high (typically above 90%), it may indicate that the device is experiencing a bottleneck and is unable to handle the current I/O load.

Let's back to the topic to discuss more details

Disk I/O Utilization: Percentage of time the disk is busy
    - %util: The percentage of time that the device is busy handling requests.

    Demo: uils 40% in this case
    ```
    $ iostat -d -k -x 1
    Device:  rrqm/s wrqm/s   r/s   w/s  rMB/s  wMB/s avgrq-sz avgqu-sz  await r_await w_await  svctm  %util
    xvda      0.00  5.00  0.00  5.00   0.00   0.00    12.80     0.01    1.60   0.00    1.60   0.80   0.40
    ```

Disk I/O Throughput includes multiple metrics, including:
    - IOPS (Input/Output Operations Per Second):
        The number of read and write operations that can be performed per second on a disk. IOPS is a measure of how many I/O operations a disk can perform in a second, and it can be used as an indicator of Disk I/O performance.
            - r/s: The number of read requests issued to the device per second.
            - w/s: The number of write requests issued to the device per second.

        If the number of read or write requests per second (r/s or w/s) consistently exceeds 80% to 90% of the maximum capacity of the disk, it may be a good idea to investigate the cause and take action to avoid performance degradation.


    - Data Transfer Rate
        The amount of data that can be read from or written to the disk per unit of time, typically measured in bytes per second (B/s) or kilobytes per second (kB/s). This metric provides an indication of the speed at which data can be transferred to and from the disk.

        - rkB/s: The number of kilobytes read from the device per second.
        - wkB/s: The number of kilobytes written to the device per second.

        If the number of kilobytes read or written per second (rkB/s or wkB/s) consistently exceeds 80% to 90% of the maximum capacity of the disk, it may be a good idea to investigate the cause and take action to avoid performance degradation.

    - Queue Length
        The number of I/O requests that are waiting to be processed by the disk. A high queue length can indicate that the disk is experiencing a backlog of requests and may be a bottleneck for Disk I/O performance.

        - aqu-sz: The average number of requests waiting in the device's queue.

        If the average queue length of pending I/O requests (aqu-sz) consistently exceeds the maximum number of requests that the disk can handle, it may indicate a bottleneck and it may be a good idea to investigate the cause and take action to avoid performance degradation.

    Demo: uils 40% in this case
    ```
    $ iostat -d -k -x 1
    Device:  rrqm/s wrqm/s   r/s   w/s  rMB/s  wMB/s avgrq-sz avgqu-sz  await r_await w_await  svctm  %util
    xvda      0.00  5.00  0.00  5.00   0.00   0.00    12.80     0.01    1.60   0.00    1.60   0.80   0.40
    xvdb      0.00  0.00 20.00  0.00   0.20   0.00    11.20     0.02    1.00   0.00    1.00   0.70   1.40

    ```

    - Latency
        The amount of time it takes for an I/O request to be completed by the disk. This metric provides an indication of the responsiveness of the disk and can affect the overall performance of the system.

        - r_await: The average amount of time (in milliseconds) that a read request takes to complete.
        - w_await: The average amount of time (in milliseconds) that a write request takes to complete.
        - svctm: The average service time (in milliseconds) for I/O requests.

        If the average amount of time that it takes for read or write requests to complete (r_await or w_await) consistently exceeds 10 milliseconds, it may be a good idea to investigate the cause and take action to avoid performance degradation.


Workload: High volume of read and write operations


Note:
```
The t2.micro instance on AWS comes with an Amazon Elastic Block Store (EBS) root volume, which is a General Purpose SSD (gp2) volume by default. According to AWS documentation, the maximum number of IOPS that a gp2 volume can support is determined by the volume size, and ranges from 100 IOPS for a 1 GB volume to 16,000 IOPS for a 1 TiB (terabyte) volume. Therefore, the maximum number of IOPS that a 1 GB gp2 volume can support is 100 IOPS.

Assuming that the workload is not heavily utilizing other system resources, the maximum number of requests that the disk can handle is approximately equal to the maximum number of IOPS. Therefore, for a 1 GB gp2 volume on a t2.micro instance, the maximum number of requests that the disk can handle is approximately 100 requests per second.

Note: 8 GB gp2 EBS volume performance

Baseline IOPS: 24 IOPS
Minimum IOPS: 100 IOPS
Maximum IOPS: 3000 IOPS


Based on these factors, it's reasonable to estimate Total Disk I/O Capacity of 8 GB gp2 EBS volume will be in the range of 100-3000 IOPS:

Total Disk I/O Capacity: Range of 100-3000 IOPS
```



We have two



#### Bandwidth Detection
For example, we have a 2 GB package on an AWS t2.micro, which is the smallest instance size in the t2 family, provides a maximum bandwidth of up to 5 Gbps for network traffic. In general, the maximum download speed you can achieve is typically around 80% of the available bandwidth.

Bandwidth: 5 Gbps
Package Size: 2GB

Formula 1:
    Available bandwidth = Bandwidth * 80%

Formula 2:
    Maximum download time = Package Size / Available bandwidth

Formula 3:
    Maximum download speed =  Package size / Maximum download time

Step 1: Calculate Available Bandwidth using Formula 1
```
    4Gbps(Available bandwidth) = 5 Gbps x  80%
```

Step 2: Calculate Maximum download time using Formula 2
```
    500 seconds(Maximum download time) = 2GB/4Gbps
```

Step 3: Calculate Maximum download speed using Formula 3
```
    4 MB/s(Maximum download speed) = 2GB/500 seconds
```

Now you got the Maximum download speed 4 MB/s, if you find you download speed achieving a Maximum download speed, and you still feel that the download speed is slow and not meeting your requirements, bandwidth is possibly the bottleneck. Consider increasing the available bandwidth for your AWS instance





<h2 id="c1"> 1. Code Techniques Level </h2>



<h2 id="c2"> 2. Web Framework Level </h2>



<h2 id="c3"> 3. Architecture Level </h2>
pass
