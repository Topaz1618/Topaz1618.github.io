---
title: "Ensuring Your Website Permanence: Techniques for Making Your Website Run Until the End of Time "
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


<!-- - [3. Code Techniques Level](#c3)
- [2. Web Framework Level](#c2)
- [3. Architecture Level](#c3) -->


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



### Database Bottleneck Detection

To check for database bottlenecks on Linux, you can use a variety of tools and techniques. Here are some steps you can take to identify and resolve database performance issues:

- Check the database logs: The database logs can provide valuable information about any errors or performance issues that are occurring. Check the logs to see if there are any error messages or warnings that indicate a problem.

- Monitor system resources: Use system monitoring tools like top, htop, or vmstat to check the CPU, memory, and disk usage of the system. If the database is using an excessive amount of resources, it could be causing performance issues.

- Use database profiling tools: Many databases have built-in profiling tools that can help you identify performance bottlenecks. For example, MySQL has the slow query log and the performance_schema database that can provide insights into slow queries and resource usage.

- Use database performance monitoring tools: There are many third-party tools available that can help you monitor and diagnose database performance issues. Some popular options include Percona Monitoring and Management (PMM), Datadog, and New Relic.

- Optimize database configuration: Check the database configuration to make sure it is optimized for your workload. Adjusting parameters like cache size and query timeouts can often improve performance.

- Optimize queries: Poorly written or inefficient queries can cause database bottlenecks. Use a query profiler like EXPLAIN to identify slow queries, and optimize them for better performance.

- Increase hardware resources: If all else fails, increasing the resources available to the database server (e.g. CPU, memory, disk space) can help alleviate performance issues.

By following these steps, you should be able to identify and resolve database bottlenecks on your Linux system.


#### SQL Queries Type

There are several types of SQL queries that can cause slow query performance, including:

- Queries that return a large number of rows: If a query returns a large number of rows, it can take longer to execute and can impact the performance of other queries running on the same database server.

- Queries that join multiple tables: Joining multiple tables can be slow, especially if the tables contain a large number of rows or if there are no indexes on the columns being joined.

- Queries that use subqueries or nested queries: Subqueries and nested queries can be slow, especially if the subquery is executed for every row in the outer query.

- Queries that use complex or inefficient WHERE clauses: WHERE clauses that use complex logic, nested conditions, or functions can be slow, especially if the columns being searched are not indexed.

- Queries that use ORDER BY or GROUP BY clauses: Sorting or grouping large result sets can be slow, especially if the columns being sorted or grouped are not indexed.

- Queries that use unoptimized or inefficient SQL code: SQL code that is not optimized or uses inefficient techniques can be slow, even if the query itself is relatively simple.

To improve query performance, it's important to optimize the database schema, use indexes, and analyze slow queries using tools like the MySQL slow query log or performance monitoring tools. Additionally, it's important to write efficient SQL code, use proper indexing techniques, and avoid unnecessary joins, subqueries, and other complex SQL constructs when possible.


#### Add index for slow SQL
- Queries that return a large number of rows: If a query returns a large number of rows, adding an index on the columns used in the WHERE clause can help improve performance. For example, if you have a table with a timestamp column and you want to retrieve all rows from the last hour, you could use the following query:
```
// Adding an index on the `timestamp` column can help improve the performance of this query.
SELECT * FROM mytable WHERE timestamp >= NOW() - INTERVAL 1 HOUR;

// This will add an index on the `timestamp` column in the `mytable` table, which can help improve the performance of queries that filter data based on this column.
ALTER TABLE mytable ADD INDEX idx_timestamp (timestamp);
```

- Queries that join multiple tables: Adding indexes on the columns used in the join condition can help improve the performance of join queries. For example, if you have a users table and an orders table that are joined on the user_id column, you could use the following query:
```
// Adding an index on the `user_id` column in the `orders` table can help improve the performance of this query.
SELECT * FROM users JOIN orders ON users.id = orders.user_id;

// This will add an index on the `user_id` column in the `orders` table, which can help improve the performance of join queries that join the `orders` and `users` tables on this column.
ALTER TABLE orders ADD INDEX idx_user_id (user_id);
```

- Queries that use subqueries or nested queries: Adding indexes on the columns used in the subquery or nested query can help improve the performance of these types of queries. For example, if you have a products table and you want to retrieve all products that have a higher price than the average price of all products, you could use the following query:
```
// Adding an index on the `price` column can help improve the performance of this query.
SELECT * FROM products WHERE price > (SELECT AVG(price) FROM products);

// This will add an index on the `price` column in the `products` table, which can help improve the performance of queries that use subqueries or [nested queries](poe://www.poe.com/_api/key_phrase?phrase=nested%20queries&prompt=Tell%20me%20more%20about%20nested%20queries.) to filter data based on this column.
ALTER TABLE products ADD INDEX idx_price (price);
```

- Queries that use complex or inefficient WHERE clauses: Adding indexes on the columns used in the WHERE clause can help improve the performance of these types of queries. For example, if you have a users table and you want to retrieve all users who are older than 30 and live in a specific city, you could use the following query:
```
// Adding indexes on the `age` and `city` columns can help improve the performance of this query.
SELECT * FROM users WHERE age > 30 AND city = 'New York';


// These commands will add indexes on the `age` and `city` columns in the `users` table, which can help improve the performance of queries that filter data based on these columns.
ALTER TABLE users ADD INDEX idx_age (age);
ALTER TABLE users ADD INDEX idx_city (city);
```

- Queries that use ORDER BY or GROUP BY clauses: Adding indexes on the columns used in the ORDER BY or GROUP BY clause can help improve the performance of these types of queries. For example, if you have a users table and you want to retrieve all users grouped by city, you could use the following query:
```
// Adding an index on the `city` column can help improve the performance of this query.
SELECT city, COUNT(*) FROM users GROUP BY city;

// This will add an index on the `city` column in the `users` table, which can help improve the performance of queries that group or sort data based on this column.
ALTER TABLE users ADD INDEX idx_city (city);
```

- Queries that use unoptimized or inefficient SQL code: Rewriting the SQL code to be more efficient or optimized can help improve the performance of these types of queries. Adding indexes on the columns used in the optimized query can further improve performance.


#### Tips


##### Composite index
Composite index  is an index that includes multiple columns. Here's an example of how to create a composite index on the col1 and col2 columns in the mytable table:

```
ALTER TABLE mytable ADD INDEX idx_col1_col2 (col1, col2);
```
This will create a composite index on the col1 and col2 columns, which means that the index will be used to optimize queries that filter, sort, or group data based on these columns. For example, the following query will benefit from the composite index:

```
SELECT * FROM mytable WHERE col1 = 'value' AND col2 = 'other_value';
```

In this case, the database engine will use the composite index to quickly find the rows that match the filter condition on col1 and col2.

In general, the database engine will choose the most efficient index to use for a given query based on the available indexes and the query's filtering, sorting, and grouping requirements. However, there may be cases where the database engine chooses an inefficient index or no index at all, in which case you can use the USE INDEX clause to specify a different index to use.

For example, consider a table with a composite index on columns col1 and col2, and a query that filters data based on col1 only:

```
SELECT * FROM mytable WHERE col1 = 'value';
```
In this case, the database engine will automatically use the composite index to optimize the query, as the index includes the col1 column. You do not need to specify the index name in the query.

However, if you have a different index on col1 alone that you know to be more efficient for this query, you could use the USE INDEX clause to force the database engine to use that index instead:

```
SELECT * FROM mytable USE INDEX (idx_col1) WHERE col1 = 'value';
```

This will force the database engine to use the idx_col1 index instead of the composite index on col1 and col2.


##### using index in Python
hen you use the filter method in SQLAlchemy, the database engine will automatically choose the most efficient index to use for the query based on the available indexes and the query conditions.

If you want to specify a specific index to use in the query, you can use the with_index method in SQLAlchemy. Here's an example:

```
results = session.query(MyModel).with_index(MyModel.idx_age).filter(MyModel.age > 30).all()
```



##### Show index of ur table
```
SHOW INDEX FROM mytable;
```

##### checking index for our table

Demo1
```
mysql> Explain SELECT * FROM mytable USE INDEX (idx_id) WHERE id <1000;
+----+-------------+---------+------------+-------+---------------+--------+---------+------+------+----------+-----------------------+
| id | select_type | table   | partitions | type  | possible_keys | key    | key_len | ref  | rows | filtered | Extra                 |
+----+-------------+---------+------------+-------+---------------+--------+---------+------+------+----------+-----------------------+
|  1 | SIMPLE      | mytable | NULL       | range | idx_id        | idx_id | 4       | NULL |  999 |   100.00 | Using index condition |
+----+-------------+---------+------------+-------+---------------+--------+---------+------+------+----------+-----------------------+
1 row in set, 1 warning (0.00 sec)
```

Demo2

```
mysql> EXPLAIN SELECT * FROM mytable WHERE id >1;
+----+-------------+---------+------------+-------+----------------+---------+---------+------+---------+----------+-------------+
| id | select_type | table   | partitions | type  | possible_keys  | key     | key_len | ref  | rows    | filtered | Extra       |
+----+-------------+---------+------------+-------+----------------+---------+---------+------+---------+----------+-------------+
|  1 | SIMPLE      | mytable | NULL       | range | PRIMARY,idx_id | PRIMARY | 4       | NULL | 1588125 |   100.00 | Using where |
+----+-------------+---------+------------+-------+----------------+---------+---------+------+---------+----------+-------------+
1 row in set, 1 warning (0.00 sec)

```
Here's a breakdown of the output from the EXPLAIN statement above:

- id column: shows the sequence in which MySQL is executing the query. In this case, there is only one row in the output, so the value is 1.
- select_type column: shows the type of query being executed. In this case, the query type is SIMPLE, which means that it's a simple SELECT statement with no subqueries or UNIONs.
- table column: shows the name of the table being queried. In this case, the table name is mytable.
- partitions column: shows whether the query is accessing a specific partition of a partitioned table. In this case, the value is NULL, which means that the query is not accessing a partitioned table.
- type column: shows the type of access method being used to retrieve the data. In this case, the access method is range, which means that MySQL is using an index to retrieve a range of rows that match the query conditions.
- possible_keys column: shows which indexes MySQL could potentially use to optimize the query. In this case, the possible keys are PRIMARY and idx_id, which are the primary key and a non-unique index on the id column, respectively.
- key column: shows which index MySQL is actually using to optimize the query. In this case, MySQL is using the PRIMARY index to optimize the query, as indicated by the value PRIMARY.
- key_len column: shows the length of the index being used, in bytes. In this case, the PRIMARY index is using 4 bytes to store the value of the id column.
- ref column: shows which columns or constants are being used with the index to retrieve the data. In this case, the value is NULL, which means that no columns or constants are being used with the index.
- rows column: shows the estimated number of rows that MySQL needs to examine to execute the query. In this case, MySQL estimates that it will need to examine 999 rows to retrieve the data.
- filtered column:  shows the percentage of rows that MySQL expects to examine, based on the query conditions and the available statistics. In
- Extra column: Using where, whichWHERE clause to filter the data





Slow query log: The slow query log is a MySQL feature that logs all queries that take longer than a specified amount of time to execute. By default, the slow query log is not enabled, so you'll need to enable it by modifying your MySQL configuration file (my.cnf or my.ini). Once the slow query log is enabled, you can review it periodically to identify queries that are taking too long to execute.
To enable the slow query log, add the following lines to your my.cnf or my.ini file:





```
slow_query_log = 1
slow_query_log_file = /path/to/slow-query.log
long_query_time = 1
```

This will enable the slow query log, specify the log file location, and set the threshold for a "slow" query to 1 second. You can adjust the long_query_time value to a different threshold as needed.

Once the slow query log is enabled, you can review it by opening the log file and looking for queries that took longer than the specified threshold to execute. You can then optimize these queries or adjust your MySQL configuration to improve performance.

Performance_schema database: The performance_schema database is a MySQL feature that provides detailed information about MySQL server performance. It contains tables that track events such as query execution, table access, and resource usage.
To use the performance_schema database, you'll need to enable it by modifying your MySQL configuration file. Here's an example of how to enable the performance_schema database:
```
performance_schema = ON
```

Once the performance_schema database is enabled, you can use SQL queries to query the various tables in the database to gather detailed information about MySQL server performance. For example, you can use the performance_schema.events_statements_summary_by_digest table to view statistics about query execution, such as the total number of queries executed, the total execution time, and the average execution time.

By using the slow query log and performance_schema database together, you can gain a comprehensive understanding of your MySQL server performance and identify bottlenecks that may be affecting performance.



```
# Time: 2022-05-01T12:34:56.789012Z
# User@Host: myuser[123.45.67.89:12345]
# Query_time: 5.123456  Lock_time: 0.000000 Rows_sent: 0  Rows_examined: 0
SET timestamp=1651439696;
SELECT * FROM mytable WHERE id = 123456;
In this example, the slow query log entry shows that a SELECT query took 5.123456 seconds to execute, with no lock time and no rows sent or examined. The log entry also shows the user and host that executed the query, the timestamp of the query, and the query itself.

```

When analyzing the slow query log, there are several values that you should pay attention to:

- Query execution time
    - This is the amount of time it takes for the query to execute, as measured by the MySQL server.
    - Threshold: Any query that takes longer than a few seconds to execute may be a candidate for optimization.

- Lock time
    - This is the amount of time that the query holds locks on tables or rows, which can impact the performance of other queries running on the same database server.
    - Threshold: Queries that hold locks on tables or rows for more than a few seconds can impact the performance of other queries running on the same database server.

- Rows examined
    - This is the number of rows that the query examines to retrieve the result set. Queries that examine a large number of rows can be slower than queries that examine a smaller number of rows.
    - Threshold: Queries that examine a large number of rows can be slower than queries that examine a smaller number of rows. You should pay attention to queries that examine more than a few thousand rows and consider optimizing them to reduce the number of examined rows.

- Index usage
    - This indicates whether the query is using indexes to retrieve the data, which can help improve performance.
    - Queries that do not use indexes or use indexes inefficiently can be slower than queries that use indexes efficiently. You should pay attention to queries that do not use indexes or have low index efficiency and consider optimizing them by adding or modifying indexes.

Regarding the performance_schema database, there are several tables and columns that you can use to identify database bottlenecks. Here are a few examples:

- performance_schema.events_statements_summary_by_digest: This table provides a summary of query execution statistics, including the number of queries executed, the total execution time, and the average execution time. You can use this table to identify queries that are taking a long time to execute and may be causing performance issues.
    - SUM_TIMER_WAIT: This column shows the total execution time for each query in microseconds. You should monitor this column to identify queries that are taking a long time to execute, as these queries may be causing performance issues. This column shows the total execution time for each query in microseconds. You might want to start paying attention to queries with a SUM_TIMER_WAIT value that exceeds a few seconds, as these queries may be taking a long time to execute and may be causing performance issues. However, the specific threshold will depend on the performance requirements of your application and the resources available on your server.

    - EXECUTIONS: This column shows the total number of times each query has been executed. You should monitor this column to identify queries that are executed frequently, as these queries may be a good target for optimization. This column shows the total number of times each query has been executed. You might want to start paying attention to queries with a high EXECUTIONS value, as these queries may be executed frequently and may be a good target for optimization. However, the specific threshold will depend on the available CPU and memory resources on your server, as well as the performance requirements of your application.

    - AVG_TIMER_WAIT: This column shows the average execution time for each query in microseconds. You should monitor this column to identify queries that are consistently slow, as these queries may be a good target for optimization.This column shows the average execution time for each query in microseconds. You might want to start paying attention to queries with an AVG_TIMER_WAIT value that exceeds a few hundred milliseconds, as these queries may be consistently slow and may be a good target for optimization. However, the specific threshold will depend on the performance requirements of your application and the acceptable response time for your users.

    performance_schema.events_statements_summary_by_digest table with some sample content:
```
+------------------------------------------------------------------------------------------------------------------------+-------------+------------+----------------+-----------------------+-----------------------+------------------------+------------------------+
| DIGEST                                                                                                                 | SCHEMA_NAME | EXEC_COUNT | SUM_TIMER_WAIT | SUM_LOCK_TIME         | SUM_ROWS_SENT         | SUM_ROWS_EXAMINED      | SUM_CREATED_TMP_DISK_TABLES |
+------------------------------------------------------------------------------------------------------------------------+-------------+------------+----------------+-----------------------+-----------------------+------------------------+------------------------+
| 5c5e5d5e5c5d5e5d5c5d5e5c5d5e5d5e5c5d5e5d5c5d5e5d5e5c5d5e5d5e5c5d5e5d5e5c5d5e5d5c5d5e5d5e5c5d5e5d5c5d5e              | test        |          1 |          30000 | 0                     |                     10 |                   1000 |                          0 |
| 5d5e5c5d5e5c5d5e5c5d5e5c5d5e5d5e5c5d5e5d5c5d5e5d5e5c5d5e5d5e5c5d5e5d5e5c5d5e5d5c5d5e5d5c5d5e5d5c5d5e              | test        |          2 |          40000 | 0                     |                     20 |                   2000 |                          0 |
| 5e5c5d5e5c5d5e5d5c5d5e5c5d5e5d5e5c5d5e5d5c5d5e5d5e5c5d5e5d5e5c5d5e5d5e5c5d5e5d5c5d5e5d5c5d5e5d5c5d5e              | test        |          3 |          50000 | 0                     |                     30 |                   3000 |                          0 |
+------------------------------------------------------------------------------------------------------------------------+-------------+------------+----------------+-----------------------+-----------------------+------------------------+------------------------+
```
In this example, the performance_schema.events_statements_summary_by_digest table includes the following columns:

- DIGEST: This column contains a hash value or fingerprint of the SQL statement.

- SCHEMA_NAME: This column contains the name of the schema where the query was executed.

- EXEC_COUNT: This column contains the number of times the query was executed.

- SUM_TIMER_WAIT: This column contains the total execution time of the query, in microseconds.

- SUM_LOCK_TIME: This column contains the total time the query spent waiting for locks, in microseconds.

- SUM_ROWS_SENT: This column contains the total number of rows sent by the query.

- SUM_ROWS_EXAMINED: This column contains the total number of rows examined by the query.

- SUM_CREATED_TMP_DISK_TABLES: This column contains the total number of temporary disk tables created by the query.

The DIGEST column in the performance_schema.events_statements_summary_by_digest table contains a hash value or fingerprint of the SQL statement, which can be difficult for humans to read and understand. However, you can use other tables in the performance_schema database, such as performance_schema.events_statements_history, to retrieve the actual SQL statements that correspond to each DIGEST value.

For example, you can use the following query to retrieve the SQL statements for the top 10 queries with the highest SUM_TIMER_WAIT value:
```
SELECT DIGEST, SCHEMA_NAME, EXEC_COUNT, SUM_TIMER_WAIT, LEFT(DIGEST_TEXT, 100) AS SQL_STATEMENT
FROM performance_schema.events_statements_summary_by_digest
JOIN performance_schema.events_statements_history ON events_statements_history.digest = events_statements_summary_by_digest.digest
ORDER BY SUM_TIMER_WAIT DESC
LIMIT 10;
```
In this query, the LEFT function is used to retrieve only the first 100 characters of the DIGEST_TEXT column, which contains the actual SQL statement.


To determine the appropriate thresholds for your environment, you should monitor the performance_schema.events_statements_summary_by_digest table over time, and use the data to identify trends and potential bottlenecks.

- performance_schema.file_summary_by_instance: This table provides information about disk I/O usage, including the number of reads and writes and the amount of data read and written. You can use this table to identify queries that are causing excessive disk I/O and may be causing performance issues.

Here are some general guidelines for the values you might want to pay attention to in this table:

- COUNT_READ: This column shows the number of read operations performed on each file handle. You might want to start paying attention to file handles with a COUNT_READ value that exceeds a few thousand, as these file handles may be experiencing a high volume of read operations and may be causing I/O bottlenecks. However, the specific threshold will depend on the performance requirements of your application and the resources available on your server.

- SUM_NUMBER_OF_BYTES_READ: This column shows the total number of bytes read from each file handle. You might want to start paying attention to file handles with a SUM_NUMBER_OF_BYTES_READ value that exceeds a few gigabytes, as these file handles may be experiencing a high volume of read operations and may be causing I/O bottlenecks. However, the specific threshold will depend on the performance requirements of your application and the resources available on your server.

- COUNT_WRITE: This column shows the number of write operations performed on each file handle. You might want to start paying attention to file handles with a COUNT_WRITE value that exceeds a few thousand, as these file handles may be experiencing a high volume of write operations and may be causing I/O bottlenecks. However, the specific threshold will depend on the performance requirements of your application and the resources available on your server.

- SUM_NUMBER_OF_BYTES_WRITE: This column shows the total number of bytes written to each file handle. You might want to start paying attention to file handles with a SUM_NUMBER_OF_BYTES_WRITE value that exceeds a few gigabytes, as these file handles may be experiencing a high volume of write operations and may be causing I/O bottlenecks. However, the specific threshold will depend on the performance requirements of your application and the resources available on your server.

- SUM_TIMER_WAIT: This column shows the total wait time in microseconds for each file handle. You might want to start paying attention to file handles with a SUM_TIMER_WAIT value that exceeds a few seconds, as these file handles may be experiencing long wait times and may be causing I/O bottlenecks. However, the specific threshold will depend on the performance requirements of your application and the resources available on your server.

To determine the appropriate thresholds for your environment, you should monitor the performance_schema.file_summary_by_instance table over time



- performance_schema.table_io_waits_summary_by_index_usage: This table provides information about table I/O waits, including the number of waits and the wait time. You can use this table to identify tables that are causing I/O bottlenecks and may be causing performance issues.

Here are some general guidelines for the values you might want to pay attention to in this table:

- COUNT_FETCH: This column shows the number of times each index was used to fetch data from disk. You might want to start paying attention to indexes with a COUNT_FETCH value that exceeds a few thousand, as these indexes may be experiencing a high volume of fetch operations and may be causing I/O bottlenecks. However, the specific threshold will depend on the performance requirements of your application and the resources available on your server.

- SUM_TIMER_FETCH: This column shows the total wait time in microseconds for each index used to fetch data from disk. You might want to start paying attention to indexes with a SUM_TIMER_FETCH value that exceeds a few seconds, as these indexes may be experiencing long wait times and may be causing I/O bottlenecks. However, the specific threshold will depend on the performance requirements of your application and the resources available on your server.

- COUNT_INSERT: This column shows the number of times each index was used to insert data into the table. You might want to start paying attention to indexes with a COUNT_INSERT value that exceeds a few thousand, as these indexes may be experiencing a high volume of insert operations and may be causing I/O bottlenecks. However, the specific threshold will depend on the performance requirements of your application and the resources available on your server.

- SUM_TIMER_INSERT: This column shows the total wait time in microseconds for each index used to insert data into the table. You might want to start paying attention to indexes with a SUM_TIMER_INSERT value that exceeds a few seconds, as these indexes may be experiencing long wait times and may be causing I/O bottlenecks. However, the specific threshold will depend on the performance requirements of your application and the resources available on your server.

- COUNT_UPDATE: This column shows the number of times each index was used to update data in the table. You might want to start paying attention to indexes with a COUNT_UPDATE value that exceeds a few thousand, as these indexes may be experiencing a high volume of update operations and may be causing I/O bottlenecks. However, the specific threshold will depend on the performance requirements of your application and the resources available on your server.

- SUM_TIMER_UPDATE: This column shows the total wait time in microseconds for each index used to update data in the table. You might want to start paying attention to indexes with a SUM_TIMER_UPDATE value that exceeds a few seconds, as these indexes may be experiencing long wait times and may be causing I/O bottlenecks. However, the specific threshold will depend on the performance requirements of your application and the resources available on your server.

To determine the appropriate thresholds for your environment, you should monitor the performance_schema.table_io_waits_summary_by_index_usage table over time, and use the data to identify trends and potential bottlenecks.

- performance_schema.memory_summary_by_instance: This table provides information about memory usage, including the amount of memory allocated and the amount of memory used. You can use this table to identify queries or processes that are using excessive memory and may be causing performance issues.
Here are some general guidelines for the values you might want to pay attention to in this table:

- CURRENT_COUNT_USED: This column shows the current number of memory blocks used by each component. You might want to start paying attention to components with a CURRENT_COUNT_USED value that exceeds a few thousand, as these components may be using a large amount of memory and may be causing memory pressure. However, the specific threshold will depend on the available memory on your server and the performance requirements of your application.

- CURRENT_NUMBER_OF_BYTES_USED: This column shows the current amount of memory used by each component, in bytes. You might want to start paying attention to components with a CURRENT_NUMBER_OF_BYTES_USED value that exceeds a few gigabytes, as these components may be using a large amount of memory and may be causing memory pressure. However, the specific threshold will depend on the available memory on your server and the performance requirements of your application.

- SUM_NUMBER_OF_BYTES_ALLOC: This column shows the total amount of memory allocated by each component, in bytes. You might want to start paying attention to components with a SUM_NUMBER_OF_BYTES_ALLOC value that exceeds a few gigabytes, as these components may be allocating a large amount of memory and may be causing memory pressure. However, the specific threshold will depend on the available memory on your server and the performance requirements of your application.

- SUM_NUMBER_OF_BYTES_FREE: This column shows the total amount of memory freed by each component, in bytes. You might want to start paying attention to components with a SUM_NUMBER_OF_BYTES_FREE value that exceeds a few gigabytes, as these components may be freeing a large amount of memory and may be causing memory fragmentation or other issues. However, the specific threshold will depend on the available memory on your server and the performance requirements of your application.

To determine the appropriate thresholds for your environment, you should monitor the performance_schema.memory_summary_by_instance table over time, and use the data to identify trends and potential memory-related issues.


<h2 id="c1"> 1. Code Techniques Level </h2>
pass

<h2 id="c2"> 2. Web Framework Level </h2>
There are several ways to check the performance of your Flask web app before and after adding a web server such as uWSGI or Gunicorn. Here are some steps you can follow:

- Use a benchmarking tool: There are several benchmarking tools available that can help you measure the performance of your web app, such as ApacheBench, Siege, or wrk. These tools allow you to send a large number of requests to your web app and measure the response times and throughput. You can use these tools to benchmark your web app before and after adding a web server to see if there is any improvement in performance.

- Monitor your web server: Once you have added a web server such as uWSGI or Gunicorn, you can use monitoring tools such as New Relic or AppDynamics to monitor the performance of your web server. These tools can help you identify any bottlenecks or performance issues and optimize your web server configuration accordingly.

- Use profiling tools: Profiling tools such as cProfile or Pyflame can help you identify any performance bottlenecks in your Flask app code. You can use these tools to profile your code before and after adding a web server to see if there is any improvement in performance.

- Use load testing tools: Load testing tools such as Locust or Gatling can help you simulate heavy traffic on your web app and measure its performance under load. You can use these tools to test your web app before and after adding a web server

- By using a benchmarking or load testing tool to simulate heavy traffic on your web app, you can test its performance under high concurrency conditions and identify any issues that may arise, such as bottlenecks in your web server, database, or message queue. This can help you optimize your system to handle high traffic loads and improve its overall performance and reliability.

- By observing the behavior of your web app under high concurrency conditions, you can identify potential issues and bottlenecks and take steps to address them, such as optimizing your database queries, adding caching layers, or scaling your infrastructure. This can help you avoid issues when you have real high concurrency traffic and ensure that your web app can handle the load without experiencing performance issues or downtime.

In summary, using benchmarking and load testing tools can help you identify and address performance issues in your web app before they become a problem, and ensure that your app can handle high concurrency traffic with ease.


Testing an API that involves multiple systems such as Redis, MySQL, and a message queue, you can use a single benchmarking tool to test the entire system and measure the overall performance. This can be more convenient and efficient than testing each system separately.

However, there are some cases where testing each system separately may be useful. For example, if you suspect that one of the systems is a performance bottleneck, testing it separately can help you identify the issue more quickly and accurately. It can also help you isolate the issue and avoid false positives or conflicting results.

Additionally, testing each system separately can help you optimize each system independently and improve its performance. For example, if you find that your Redis cache is slow or unable to handle the load, you can optimize it separately from your message queue or MySQL database.
