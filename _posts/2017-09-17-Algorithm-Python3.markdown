---
title: "[CN] Top 5 排序算法"
layout: post
date: 2017-09-17 22:48
tag:
 - Algorithms
 - CN
blog: true
author: Topaz
summary: "冒泡排序 选择排序 插入排序 希尔排序 二分法"
permalink: Algorithm-Python
---
<h1 class="title">[CN] Top 5 排序算法  </h1>

<h2> Table of Contents </h2>
- [冒泡排序](#c1)
- [选择排序](#c2)
- [插入排序](#c3)
- [希尔排序](#c4)
- [二分法](#c5)


<h2 id="c1"> 冒泡排序 </h2>
{% highlight python %}
 list = [88,76,44,29,10]
 for i1 in range(len(list)-1):   #0-3
 	# print(i1)
 	for i2 in range(len(list)-i1-1):    #0-3
 		if list[i2] > list[i2+1]:
 			# print(list[i2],list[i2+1])
 			tmp = list[i2+1]
 			list[i2+1] = list[i2]
 			list[i2] = tmp
{% endhighlight %}

<h2 id="c2"> 选择排序 </h2>
版本一：
{% highlight python %}
 data_set = [9, 1, 22, 31, 45, 3, 6, 2, 11]
 smallest_num_index = 0  
 loop_count = 0
 for j in range(len(data_set) - 1):
 	print('列表', smallest_num_index)
 	for i in range(j + 1, len(data_set)):  
 		if data_set[i] < data_set[smallest_num_index]:  
 			smallest_num_index = i  
 		loop_count += 1
 	else:  
 		print("====== 第 %s 次循环，找到倒数第 %s 最小值 %s 最小值下标 %s ======" % (j + 1, j + 1, data_set[ smallest_num_index],smallest_num_index))
 		tmp = data_set[smallest_num_index]  
 		data_set[smallest_num_index] = data_set[j]
 		data_set[j] = tmp  
 	print(data_set)
{% endhighlight %}

版本二：
{% highlight python %}
 list = [99,89,38,77,23,16,9]
 print(len(list))
 for i1 in range(len(list)-1):    
 	for i2 in range(i1+1,len(list)):
 		if list[i2] < list[i1]:     
 			tmp = list[i2]      
 			list[i2] = list[i1]
 			list[i1] = tmp     
 print(list)
{% endhighlight %}

<h2 id="c3"> 插入排序 </h2>
{% highlight python %}
 list = [88,76,44,29,10]
 source = [92, 77, 67, 8, 6, 84, 55, 85, 43, 67]
 for index in range(1, len(source)):
 	current_val = source[index]  
 	position = index    
 	print(source)
 	while position > 0 and source[position - 1] > current_val:
 		print("Position: %s  右边值>: %s  " % (position,source[position - 1] ))
 		source[position] = source[position - 1]  
 		position -= 1  
 	source[position] = current_val list
{% endhighlight %}

<h2 id="c4"> 希尔排序 </h2>
{% highlight python %}
 import time, random
 source = [random.randrange(10 + i) for i in range(10)]
 print(source)
 interval = int(len(source) / 2)  
 print('间隔',interval)
 while interval > 0:
 	print("---interval ---", interval)  
 	for index in range(len(source)):   
 		if index + interval < len(source):
 			current_val = source[index]  # 当前下标元素
 			if current_val > source[index + interval]:
 				source[index], source[index + interval] = source[index +interval], source[index]
 	interval = int(interval / 2)
 else:  
 	for index in range(1, len(source)):
 		current_val = source[index]  
 		position = index
 		while position > 0 and source[position - 1] > current_val:  
 			source[position] = source[position - 1]  
 			position -= 1
 		source[position] = current_val
 	print(source)
{% endhighlight %}


<h2 id="c5"> 二分法 </h2>
版本一：
{% highlight python %}
 def tu(list,arg):
     min = 0
     max = len(list) - 1
     while min <= max:
         mid = int((min+max)/2)
         m = list[mid]
         if arg > m:
             print("查找的数 %s 在当前中位数 %s 右边"%(arg,m))
             min = mid+1
         elif arg < list[mid]:
             print("查找的数 %s 在当前中位数 %s 左边"%(arg,m))
             max = mid - 1
         else:
             print("是他",mid)
             return (mid)

 if __name__ == "__main__":
     list = [2, 1, 5, 3, 7]
     list.sort()	 
     print(list)
     tu(list,6)
{% endhighlight %}

版本二：
{% highlight python %}
 data = [1, 3, 6, 7, 9, 12, 14, 16, 17, 18, 20, 21, 22, 23, 30, 32, 33, 35]
 def binary_search(dataset,find_num):
 	print(dataset)
 	if len(dataset) >1:
 		mid = int(len(dataset)/2)
 		if dataset[mid] == find_num:  #find it
 			print("找到数字",dataset[mid])
 		elif dataset[mid] > find_num :# 找的数在mid左面
 			print("\033[31;1m找的数在mid[%s]左面\033[0m" % dataset[mid])
 			return binary_search(dataset[0:mid], find_num)
 		else:# 找的数在mid右面
 			print("\033[32;1m找的数在mid[%s]右面\033[0m" % dataset[mid])
 			return binary_search(dataset[mid+1:],find_num)
 	else:
 		if dataset[0] == find_num:  #find it
 			print("找到数字啦",dataset[0])
 		else:
 			print("没的分了,要找的数字[%s]不在列表里" % find_num)
 binary_search(data,66)
{% endhighlight %}

<h2 > 随机赠送斐波那契数列 </h2>
{% highlight python %}
 def fab(max):
 	n, a, b = 0, 0, 1
 	while n < max:
 		print b
 		a, b = b, a + b
 		n = n + 1
{% endhighlight %}
