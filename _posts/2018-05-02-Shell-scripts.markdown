---
title: "[CN] Shell 脚本实例"
layout: post
date: 2015-03-24 22:48
tag:
 - Bash Shell
 - Script
 - Linux
 - CN
blog: true
author: Topaz
summary: "基于 Bash shell 的一键安装脚本，服务启停脚本，远程分发脚本等"
permalink: shell-scripts
---
<h1 class="title"> Shell 脚本汇总 </h1>


<h2> Table of Contents </h2>
- [服务一键启停脚本](#c1)
- [Rsync 推送数据备份脚本](#c2)
- [Zabbix agent 一键安装脚本](#c3)
- [Rsync 服务一键安装脚本](#c4)
- [Tomcat 日志删除脚本](#c5)
- [ssh批量分发脚本](#c6)


<h2 id="c1"> 服务一键启停脚本 </h2>

{% highlight bash %}
#!/bin/bash
. /etc/init.d/functions
Ip="xxx.xxx.xxx.xx"
Redis="/usr/local/redis-3.2.0/src"

 ########### Tomcat Start/Stop Function ################
tomcat () {
read -p "Enter {p2p|shop}:" tname
if [ "$2" == "start" ];then
	/data/apache-tomcat-7.0.69-${tname}/bin/startup.sh &>/dev/null
else
	/data/apache-tomcat-7.0.69-${tname}/bin/shutdown.sh &>/dev/null
[ $? -eq  0 ] && action "Tomcat is ok!!" /bin/true
fi
}

 ########### Redis Start/Stop Function ##################
redis(){
if [ "$2" == "start" ];then
	for i in {7000..7005}
	do
		cd /data/recluster/$i
		rm -f appendonly.aof  dump.rdb  nodes.conf
	done
	/usr/local/redis-3.2.0/bin/redis-server redis.conf
	$Redis/redis-trib.rb  create  --replicas 1 $Ip:7000 $Ip:7001 $Ip:7002 $Ip:7003 $Ip:7004 $Ip:7005
else
	/usr/local/redis-3.2.0/bin/redis-cli shutdown
fi
}

 ########### Mysql Start/Stop Function ##################
mysql(){
if [ "$2" == "start" ];then
	/etc/init.d/mysqld start
else
	/etc/init.d/mysqld stop
fi
}

 ########### Mq Start/Stop Function ##################
mq(){
if [ "$2" == "start" ];then
	rabbitmq-server -detached
else
	rabbitmqctl stop
fi
}

 ########### Nginx Start/Stop Function ##################
nginx(){
if [ "$2" == "start" ];then
	/etc/init.d/nginx start
else
	/etc/init.d/mysqld stop
fi
}


main(){
if [  -z "$1" ];then
echo "Usage:  sh $0 {start|stop}"
exit
fi
case $2 in
	start)
	#Startup sequence: mysql -> nginx -> redis -> mq -> tomcat
		case $1 in
			all)
				mysql &&\
				nginx &&\
				redis &&\
				mq	&&\
				tomcat	&&\
				action "mysql&&nginx&&redis&&mq&&tomcat start is ok！！！" /bin/true
				;;
			*)
				$1
				;;
		esac
		;;
	stop)
	#Closing sequence: tomcat -> mq -> redis -> mysql -> nginx
		case $1 in
			all)
				tomcat &&\
				mq &&\
				redis &&\
				mysql &&\
				nginx &&\
				action "mysql&&nginx&&redis&&mq&&tomcat stop is ok！！！" /bin/true
				;;
			*)
				$1
				;;
		esac
		;;
esac
}

main $1 $2
{% endhighlight %}

<h2 id="c2">Rsync 推送数据备份脚本</h2>
{% highlight bash %}
#!/bin/bash
#create by Topaz
. /etc/init.d/funions
Bak='/Release/mysql'
Date=`date +%Y%m%d`
Server_ip='xxx.xxx.xxx.xx'
User='rsync_backup'
Passwd_file='/etc/rsync.password'
for i in {p2p,dfhxshop,binlog}
do
	cd $Bak/$i &&\
	rsync -avz $Date.tar.gz $User@$Server_ip::backup/mysql/`hostname`/$i/ --password-file=$Passwd_file &>/dev/null &&\
	action "$i push success ! " /bin/true

done
{% endhighlight %}

<h2 id="c3">Zabbix agent 一键安装脚本 </h2>
{% highlight bash %}
#!/bin/bash
reap -p "Enter host ip: " ip
cd /home/topaz/tools/
tar xf zabbix-3.0.1.tar.gz
groupadd zabbix
useradd -g zabbix zabbix
yum install gcc-c++ -y
./configure --prefix=/usr/local/zabbix --enable-agent
sleep 3
make install

mkdir /var/log/zabbix
mkdir /usr/local/zabbix/var
chown -R zabbix:zabbix /usr/local/zabbix
chown -R zabbix:zabbix /var/log/zabbix
cp misc/init.d/fedora/core/zabbix_agentd /etc/init.d/
chmod 755 /etc/init.d/zabbix_agentd
sed -i 's#BASEDIR=/usr/local#BASEDIR=/usr/local/zabbix#g' /etc/init.d/zabbix_agentd

cd /usr/local/zabbix/etc
cp zabbix_agentd.conf zabbix_agentd.conf.ori
egrep -v '^$|^#' /usr/local/zabbix/etc/zabbix_agentd.conf.ori >zabbix_agentd.conf
sed -i 's#Server=127.0.0.1#Server=xxx.xxx.xxx.x#g' zabbix_agentd.conf
sed -i 's#Hostname=Zabbix server#Hostname=xxx.xxx.xxx.xx#g' zabbix_agentd.conf
chkconfig zabbix_agentd on
/etc/init.d/zabbix_agentd start

{% endhighlight %}
<h2 id="c4">Rsync 一键安装脚本</h2>
{% highlight bash %}
#!/bin/bash
#this create bytopaz
#date: 2016-7-29

 ###############int################
Rsync_proc=`netstat -lntup|grep rsync|wc -l`
Check_rpm=`rpm -qa rsync|wc -l`
Rsync_user=rsync_backup
Password="123456"
Password_file=/etc/rsync.password


 ###############Check Rsync################
[ -f /etc/init.d/functions ]&& . /etc/init.d/functions
[ $Rsync_proc -ne 0 ]&&{
action "/var/run/rsyncd.pid: File exists" /bin/false
exit 1
}

 ###############Rsync yum################
if [ $Check_rpm -eq 1 ]
then
 action "Package rsync already installed and latest version" /bin/false
else
 echo "YUM INSTALL RSYNC..."
 yum -y install rsync >>/tmp/rsync_install.log &&\
 echo ==================`date +%F`=================== >>/tmp/rsync_install.log &&\
 [ $? -eq 0 ] && action "Complete!" /bin/true
fi

 ###############Rsync configure################
if [ ! -f /etc/rsyncd.conf ]
then
cat >/etc/rsyncd.conf <<EOF
 #Rsync server
 ##rsyncd.conf start##
 uid = rsync
 gid = rsync
 use chroot = no
 max connections = 200
 timeout = 600
 pid file = /var/run/rsyncd.pid
 lock file = /var/run/rsync.lock
 log file = /var/log/rsyncd.log
 ignore errors
 read only = false
 list = false
 hosts allow = 192.168.0.0/24
 hosts deny = 0.0.0.0/32
 auth users = $Rsync_user
 secrets file = $Password_file
 #####################################
 [backup]
 path = /Release/rsync
EOF
 [ $? -eq 0 ] && action "configure is Complete" /bin/true
 fi

 ###############Create################
rsync --daemon && action "rsync --daemon is success!!" /bin/true
[ ! -f /Release/rsync ] && /bin/mkdir /Release/rsync
id rsync &>/dev/null
if [ $? -eq 1 ]
then
 useradd rsync -s /sbin/nologin -M &&\
 action "useradd 'rsync' is ok" /bin/true
else
 action "useradd: user 'rsync' already exists" /bin/false
fi
echo "$Rsync_user:$Password" >$Password_file &&\
chmod 600 $Password_file &&\
chown -R rsync.rsync /Release/rsync &&\
action "Welcome to the Rsync!!" /bin/true

 ############### client example ####################
#ll /etc/rsync.password
#-rw------- 1 root root 7 Jul 29 10:07 /etc/rsync.password
#cat /etc/rsync.password
#123456
{% endhighlight %}


<h2 id="c5">Tomcat 日志删除 </h2>
{% highlight bash %}
#!/bin/bash
for i in {p2p,shop}
do
	cd /data/$i-tomcat/logs
	/usr/bin/find -name "*.log" -mtime +7|xar rm -f
{% endhighlight %}


<h2 id="c6">ssh批量分发脚本 </h2>
{% highlight bash %}
#!/bin/bash
. /etc/init.d/functions				#调用函数，在后面判断是否成功时会调用它里面的模块
[ $# -ne 1 ] && {					
        echo "USAGE:sh $0 ARG"		
        exit 1						
}
for n in 138 139					#主机多的话也可以从文件里读取
do
        scp -P22 $1 dog@10.0.0.$i:~ &>/dev/null
		if [ $? -eq 0 ]				
        then
                action "10.0.0.$i fenfa $1 is ok" /bin/true		#成功的话提示，并调用成功提示模块
        else
                action "10.0.0.$i fenfa $1 is false" /bin/false	#失败的话提示，并调用失败提示模块
        fi
done
{% endhighlight %}
