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
permalink: ensure-your-website-permanence
---
<h1 class="title"> Ensuring Your Website Permanence: Techniques for Making Your Website Run Until the End of Time </h1>



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
For example, we have a database running on an AWS EC2 instance with with a high volume of read and write operations.

In general, the maximum Disk I/O throughput you can achieve is dependent on the Disk I/O capacity and the Workload demand of the application.
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

Disk I/O Utilization: Percentage of time the disk is busy
- %util: The percentage of time that the device is busy handling requests. If the %util value is consistently higher than 90%, it may indicate that the device is a bottleneck for Disk I/O activity

Disk I/O Throughput includes multiple metrics, including:
- IOPS (Input/Output Operations Per Second):
    The number of read and write operations that can be performed per second on a disk. IOPS is a measure of how many I/O operations a disk can perform in a second, and it can be used as an indicator of Disk I/O performance.
        - r/s: The number of read requests issued to the device per second.
        - w/s: The number of write requests issued to the device per second.

    Number of read/write requests per second (r/s or w/s) consistently exceeds 80% to 90% of the maximum capacity of the disk, Consider investigate the cause and take action to avoid performance degradation.


- Data Transfer Rate
    The amount of data that can be read from or written to the disk per unit of time, typically measured in bytes per second (B/s) or kilobytes per second (kB/s). This metric provides an indication of the speed at which data can be transferred to and from the disk.

    - rkB/s: The number of kilobytes read from the device per second.
    - wkB/s: The number of kilobytes written to the device per second.

    Number of kilobytes read/written per second (rkB/s or wkB/s) consistently exceeds 80% to 90% of the maximum capacity of the disk. Consider investigate

- Queue Length
    The number of I/O requests that are waiting to be processed by the disk. A high queue length can indicate that the disk is experiencing a backlog of requests and may be a bottleneck for Disk I/O performance.

    - aqu-sz: The average number of requests waiting in the device's queue.

    If the average queue length of pending I/O requests (aqu-sz) consistently exceeds the maximum number of requests that the disk can handle, it may indicate a bottleneck and it may be a good idea to investigate the cause and take action to avoid performance degradation. for a 8 GB gp2 volume on a t2.micro instance,the maximum number of IOPS that a 1 GB gp2 volume can support is 100 IOPS. the maximum number of requests that the disk can handle is approximately 100 requests per second.

- Latency
    The amount of time it takes for an I/O request to be completed by the disk. This metric provides an indication of the responsiveness of the disk and can affect the overall performance of the system.

    - r_await: The average amount of time (in milliseconds) that a read request takes to complete.
    - w_await: The average amount of time (in milliseconds) that a write request takes to complete.
    - svctm: The average service time (in milliseconds) for I/O requests.

    If the average amount of time that it takes for read or write requests to complete (r_await or w_await) consistently exceeds 10 milliseconds, it may be a good idea to investigate the cause and take action to avoid performance degradation.


Another thing, the iostat command can provide Disk I/O statistics for each device attached to the system. In this example, we do have two devices(loop0 and loop4) attached to system, use lsof commands to identify which devices are associated with specific workloads or applications.
```
$ sudo lsof /dev/loop2
COMMAND PID USER  FD   TYPE DEVICE SIZE/OFF NODE NAME
snapd   804 root txt    REG    7,4 32485984  150 /snap/snapd/17883/usr/lib/snapd/snapd
```

Let's say /snap/snapd/17883/usr/lib/snapd/snapd is our appliction that need to check bottleneck, and you can see snapd process has a file descriptor (FD) open on the /dev/loop2 device, /dev/loop2 using by it😊. now go back to check value on device loop4. Let's say check %util. If the %util value for a device is consistently high (typically above 90%), it may indicate that the device is experiencing a bottleneck and is unable to handle the current I/O load.


Workload: High volume of read and write operations, a few key values in the iostat output that can indicate workload:

- tps (transactions per second): This value indicates the number of read and write operations per second that are being performed on the disk. A sustained tps value above 100-200 can indicate a heavy workload on the system.

- kB_read/s and kB_wrtn/s (kilobytes read and written per second): These values indicate the amount of data being read from and written to the disk per second. A sustained kB_read/s or kB_wrtn/s value above 10-20 MB/s can indicate a heavy workload on the system.

- %util (utilization percentage): This value indicates the percentage of time that the disk is busy handling I/O requests. A high %util value can indicate a high workload on the disk.

```
$ iostat -d -p loop4 1
Linux 5.15.0-56-generic (VM-0-6-ubuntu) 	04/22/23 	_x86_64_	(2 CPU)

Device             tps    kB_read/s    kB_wrtn/s    kB_dscd/s    kB_read    kB_wrtn    kB_dscd
loop4             0.23         8.05         0.00         0.00      74554          0          0

```

### Network Congestion Detection
To check for network congestion, you can use various tools and techniques depending on the specific network and environment you are working with. Here are a few options:

- Ping: You can use the ping command to test the connectivity and latency between two network devices. If you see high latency or packet loss, it may indicate network congestion. For example, you can use the following command to ping a remote server and see the round-trip time
```
$ ping 8.8.8.8
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=58 time=0.632 ms
--- 8.8.8.8 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1002ms
rtt min/avg/max/mdev = 0.632/0.647/0.662/0.015 ms

- ttl: Number of router hops that a packet can make before it is discarded. The default TTL value for most operating systems is 64. If you consistently see TTL values that are much lower than 64 or decreasing over time, it may indicate that packets are being dropped due to congestion or other issues, there are issues with the routing path or network configuration.

- RTT(Round-trip time): This is the amount of time it takes for a packet to travel from your device to the remote device and back. The RTT is usually measured in milliseconds (ms).  In general, an average RTT of less than 100ms is considered good for a local network, while an average RTT of less than 500ms is considered good for a global network. If the average RTT is consistently higher than these values, it may indicate network congestion or latency issues.

- Packet loss: This is the percentage of packets that are lost during the transmission. As a general rule of thumb, packet loss of less than 1% is considered acceptable for most networks. If the packet loss rate is consistently higher than 1%, it may indicate network congestion or other issues.
```


- Traceroute: You can use the traceroute command to trace the network path between your device and a remote server, and see the latency and packet loss at each hop. If you see high latency or packet loss at a particular hop, it may indicate network congestion. For example, you can use the following command to traceroute to a remote server

```
$ sudo apt install traceroute   

$ traceroute google.com
traceroute to google.com (142.250.204.78), 30 hops max, 60 byte packets
 1  * 11.84.200.225 (11.84.200.225)  0.563 ms 11.84.200.193 (11.84.200.193)  0.409 ms
 2  * * *
 3  * * *
 4  10.196.94.161 (10.196.94.161)  1.080 ms 10.162.32.86 (10.162.32.86)  1.362 ms 10.196.94.137 (10.196.94.137)  1.007 ms
 5  10.162.32.98 (10.162.32.98)  0.875 ms 72.14.223.150 (72.14.223.150)  2.603 ms 10.162.32.98 (10.162.32.98)  1.378 ms
 6  72.14.222.196 (72.14.222.196)  1.515 ms  1.265 ms 108.170.241.1 (108.170.241.1)  1.280 ms
 7  * 108.170.241.1 (108.170.241.1)  1.255 ms  1.136 ms
 8  108.170.233.20 (108.170.233.20)  0.678 ms 72.14.232.155 (72.14.232.155)  1.261 ms 72.14.235.205 (72.14.235.205)  1.342 ms
 9  108.170.241.80 (108.170.241.80)  0.940 ms 209.85.142.193 (209.85.142.193)  2.145 ms 216.239.63.216 (216.239.63.216)  14.264 ms
10  hkg07s39-in-f14.1e100.net (142.250.204.78)  1.394 ms  1.318 ms 216.239.63.216 (216.239.63.216)  1.715 ms

- hops: This is the maximum number of hops that the traceroute command will send before stopping. In this example, we are allowing up to 30 hops.

- packets: This is the packet size being sent in each hop. In this example, the packet size is 60 bytes.

- Latency: This is the response time or round-trip time for each hop along the path. A high latency value can indicate network congestion or other issues. The latency is usually measured in milliseconds (ms). The average latency of more than 100 milliseconds (ms) is considered high for a local network, while an average latency of more than 500ms is considered high for a global network.

- Packet loss: Some versions of traceroute also include packet loss statistics for each hop. A high packet loss rate can indicate network congestion or other issues.

- Router information: The output of traceroute also includes information about each router along the path, including its IP address and hostname. This information can be useful for troubleshooting network connectivity issues.

- hkg07s39-in-f14.1e100.net (142.250.204.78): Destination in this case(A Google server located in Hong Kong)

- *: In this case line2, line3 lines indicate that the traceroute did not receive a response from that hop within the allotted time.

```



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


### Server Load Detection
There are a few ways to check the server load on a Linux-based system. Here are some common methods:

- top: The top command is a popular tool for monitoring system performance in real-time. To check the server load with top, simply run the command and look at the load averages at the top of the output. The load averages show the number of processes that are waiting for CPU time, and are displayed as three values representing the load over the past 1, 5, and 15 minutes, respectively. If the load averages exceed the number of CPU cores on the system, it may indicate that the server is under heavy load and may be experiencing performance issues.

- uptime: The uptime command is a simple tool that displays the current time, how long the system has been running, and the load averages for the past 1, 5, and 15 minutes. The load averages are displayed in the same format as in the top command, and can be used to check the server load.

- sar: The sar command is a system activity report tool that can be used to collect and analyze system performance data over time. To check the server load with sar, simply run the command with the -q flag to display the load averages for the past day, and look for any spikes or trends in the load averages that may indicate server load issues.

- vmstat: The vmstat command is a tool that provides information about system memory usage, CPU usage, and other system statistics. To check the server load with vmstat, run the command with the -n flag to disable the header, and look at the r column in the output, which shows the number of processes waiting for CPU time. If the r value is consistently high, it may indicate that the server is under heavy load and may be experiencing performance issues.


<h2 id="c1"> 1. Code Techniques Level </h2>



<h2 id="c2"> 2. Web Framework Level </h2>



<h2 id="c3"> 3. Architecture Level </h2>
pass
