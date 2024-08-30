---
title: "[CN] Python 监控脚本实例"
layout: post
date: 2015-04-07 13:15
tag:
 - Monitor
 - Script
 - Bash Shell
 - DevOps
 - Python
 - CN
tale: true
author: Topaz
summary: "Python2 和 Python3两个版本的监控脚本，包含对CPU, Disk, Load, Memory, MySQL 和 Network 的监控"
permalink: monitor-scripts
---
<h1 class="title"> 监控脚本实例 (Python) </h1>


<h2> Table of Contents</h2>
- [CPU](#c1)
- [Disk](#c2)
- [Load](#c3)
- [Memory](#c4)
- [Mysql](#c5)
- [Network](#c6)


<h2 id="c1"> CPU 监控 </h2>
Python 3.x 版本
{% highlight python %}
import psutil
def monitor(frist_invoke=1):
    cpu_message = psutil.cpu_times()
    value_dic= {
        'user': cpu_message.user,
        'nice': cpu_message.nice,
        'system': cpu_message.system,
        'idle': cpu_message.idle,
}
    return value_dic
if __name__ == '__main__':
    print(monitor())

{% endhighlight %}

Python 2.x 版本
{% highlight python %}
import commands
def monitor(frist_invoke=1):
    shell_command = 'sar 1 3| grep "^Average:"'
    status,result = commands.getstatusoutput(shell_command)
    #print result.split()[2:]
    user,nice,system,iowait,steal,idle = result.split()[2:]
    value_dic= {
        'user': user,
        'nice': nice,
        'system': system,
        'idle': idle,
}
    return value_dic
if __name__ == '__main__':
    print monitor()

{% endhighlight %}
<h2 id="c2">Disk 监控</h2>
{% highlight python %}
import psutil
value_dic = {'part':{},'io':{}}
def monitor():
    parts = psutil.disk_partitions()    #磁盘全部信息
    disk_io = psutil.disk_io_counters() #磁盘io读写
    disk_parts_io = psutil.disk_io_counters(perdisk=True)
    value_dic['io']={
        'read_count':disk_io.read_count,
        'write_count':disk_io.write_count,
        'read_bytes':disk_io.read_bytes,
        'write_bytes ':disk_io.write_bytes,
    }
    for part in parts:
        usage = psutil.disk_usage(part.mountpoint)  #磁盘利用率
        value_dic['part'][part.device] = {
            'device':part.device,
            'mountpoint':part.mountpoint,
            'fstype':part.fstype,
            'opts':part.opts,
            'usage':{
                'total': usage.total,
                'used': usage.used,
                'free': usage.free,
                'percent':usage.percent,
            }
        }
    '''
    监控单个分区io读写信息
    for part_io,message in disk_parts_io.items():
        print(part_io,message.read_count,message.read_bytes,message.write_count,message.write_bytes)
    '''
    return value_dic
if __name__ == "__main__":
    print(monitor())
{% endhighlight %}

<h2 id="c3"> Load  </h2>
Python 3.x 版本
{% highlight python %}
import subprocess
def monitor():
    shell_command = 'uptime'
    status,result = subprocess.getstatusoutput(shell_command)
    load1,load5,load15 = result.split('load average:')[1].split(',')
    #print(load1,load5,load15)
    value_dic= {
            'load1': load1,
            'load5': load5,
            'load15': load15,
    }
    return value_dic
if __name__ == "__main__":
    print(monitor())
{% endhighlight %}

Python 2.x 版本
{% highlight python %}
import commands
def monitor():
    shell_command = 'uptime'
    status,result = commands.getstatusoutput(shell_command)
    load1,load5,load15 = result.split('load average:')[1].split(',')
    #print(load1,load5,load15)
    value_dic= {
            'load1': load1,
            'load5': load5,
            'load15': load15,
    }
    return value_dic
if __name__ == "__main__":
    print monitor()

{% endhighlight %}

<h2 id="c4">Memory</h2>
{% highlight python %}
import psutil
def monitor():
    mem = psutil.virtual_memory()
    mem_swap = psutil.swap_memory()
    mem_dic = { 'MemTotal': mem.total,
            'MemUsage': mem.used,
            'Cached': mem.cached,
            'MemFree':mem.free ,
            'Buffers': mem.buffers,
            'SwapFree': mem_swap.free,
            'SwapUsage': mem_swap.used,
            'SwapTotal': mem_swap.total,
            }
    return mem_dic
if __name__ == '__main__':
    print(monitor())
{% endhighlight %}

<h2 id="c5">Mysql  </h2>
Python 3.x 版本
{% highlight python %}
#_*_coding:utf-8_*_
# Author:Topaz
import subprocess
class MyssqlMonitor(object):
    def __init__(self):
        self.user = 'topaz'
        self.passwd = '123456'
        self.value_dic = {}
    def get_value(self):
        key_list = ['Com_insert', 'Com_update', 'Com_delete', 'Com_select']
        for key in key_list:
            temp, last = subprocess.getstatusoutput(
                "mysqladmin -u%s -p%s extended-status|grep '\<%s\>'|cut -d'|' -f3"
                %(self.user,self.passwd,key))
            if key == 'Com_insert':
                self.value_dic['insert'] = int(last)
            elif key == 'Com_update':
                self.value_dic['update'] = int(last)
            elif key == 'Com_delete':
                self.value_dic['delete'] = int(last)
            else:
                self.value_dic['select'] = int(last)
        self.mysql_tps()
        self.mysql_qps()
        return self.value_dic
    def mysql_tps(self):
        tps = self.value_dic['insert'] +self.value_dic['update'] + self.value_dic['delete']
        self.value_dic['tps'] = tps
    def mysql_qps(self):
        qps = self.value_dic['insert'] +self.value_dic['update'] + self.value_dic['delete'] + self.value_dic['select']
        self.value_dic['qps'] = qps
if __name__ == "__main__":
    MyssqlMonitor().get_value()
{% endhighlight %}

Python 2.x 版本
{% highlight python %}
#_*_coding:utf-8_*_
# Author:Topaz
import commands
class MyssqlMonitor(object):
    def __init__(self):
        self.user = 'topaz'
        self.passwd = '123456'
        self.value_dic = {}
    def get_value(self):
        key_list = ['Com_insert', 'Com_update', 'Com_delete', 'Com_select']
        for key in key_list:
            temp, last = commands.getstatusoutput(
                "mysqladmin -u%s -p%s extended-status|grep '\<%s\>'|cut -d'|' -f3"
                %(self.user,self.passwd,key))
            if key == 'Com_insert':
                self.value_dic['insert'] = int(last)
            elif key == 'Com_update':
                self.value_dic['update'] = int(last)
            elif key == 'Com_delete':
                self.value_dic['delete'] = int(last)
            else:
                self.value_dic['select'] = int(last)
        self.mysql_tps()
        self.mysql_qps()
        return self.value_dic
    def mysql_tps(self):
        tps = self.value_dic['insert'] +self.value_dic['update'] + self.value_dic['delete']
        self.value_dic['tps'] = tps
    def mysql_qps(self):
        qps = self.value_dic['insert'] +self.value_dic['update'] + self.value_dic['delete'] + self.value_dic['select']
        self.value_dic['qps'] = qps
if __name__ == "__main__":
    print MyssqlMonitor().get_value()

{% endhighlight %}
<h2 id="c6">Network </h2>
Python 3.x 版本
{% highlight python %}
import psutil
value_dic ={'data':{}}
def monitor():
    devices_message = psutil.net_io_counters(pernic=True)   #每个网络接口的io信息
    for device_name,device_message in devices_message.items():
        value_dic['data'][device_name] = {
            'bytes_sent':device_message.bytes_sent,
            'bytes_recv':device_message.bytes_recv,
            'packets_sent':device_message.packets_sent,
            'packets_recv':device_message.packets_recv,
        }
    return value_dic
if __name__ == "__main__":
   print(monitor())
{% endhighlight %}


Python 2.x 版本
{% highlight python %}
#_*_coding:utf-8_*_
# Author:Topaz
import commands
class MyssqlMonitor(object):
    def __init__(self):
        self.user = 'topaz'
        self.passwd = '123456'
        self.value_dic = {}
    def get_value(self):
        key_list = ['Com_insert', 'Com_update', 'Com_delete', 'Com_select']
        for key in key_list:
            temp, last = commands.getstatusoutput(
                "mysqladmin -u%s -p%s extended-status|grep '\<%s\>'|cut -d'|' -f3"
                %(self.user,self.passwd,key))
            if key == 'Com_insert':
                self.value_dic['insert'] = int(last)
            elif key == 'Com_update':
                self.value_dic['update'] = int(last)
            elif key == 'Com_delete':
                self.value_dic['delete'] = int(last)
            else:
                self.value_dic['select'] = int(last)
        self.mysql_tps()
        self.mysql_qps()
        return self.value_dic
    def mysql_tps(self):
        tps = self.value_dic['insert'] +self.value_dic['update'] + self.value_dic['delete']
        self.value_dic['tps'] = tps
    def mysql_qps(self):
        qps = self.value_dic['insert'] +self.value_dic['update'] + self.value_dic['delete'] + self.value_dic['select']
        self.value_dic['qps'] = qps
if __name__ == "__main__":
    print MyssqlMonitor().get_value()
{% endhighlight %}
