---
title: "Top 5 Sorting Algorithms"
layout: post
date: 2017-09-17 22:49
tag:
 - Algorithms
 - CN
blog: true
author: Topaz
summary: "Bubble Sort, Selection Sort, Insertion Sort, Shell Sort, Binary search algorithm, Fibonacci"
permalink: Algorithm-Python-en
---
<h1 class="title">Top 5 Sorting Algorithms  </h1>

<h2> Table of Contents </h2>
- [Bubble Sort](#c1)
- [Selection Sort](#c2)
- [Insertion Sort](#c3)
- [Shell Sort](#c4)
- [Binary search algorithm](#c5)


<h2 id="c1"> Bubble Sort </h2>
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

<h2 id="c2"> Selection Sort </h2>
Method 1：
{% highlight python %}
 data_set = [9, 1, 22, 31, 45, 3, 6, 2, 11]
 smallest_num_index = 0  
 loop_count = 0
 for j in range(len(data_set) - 1):
 	print('List', smallest_num_index)
 	for i in range(j + 1, len(data_set)):  
 		if data_set[i] < data_set[smallest_num_index]:  
 			smallest_num_index = i  
 		loop_count += 1
 	else:  
 		print("====== idx %s ，Find the %s smallest number, value %s index %s ======" % (j + 1, j + 1, data_set[ smallest_num_index],smallest_num_index))
 		tmp = data_set[smallest_num_index]  
 		data_set[smallest_num_index] = data_set[j]
 		data_set[j] = tmp  
 	print(data_set)
{% endhighlight %}

Method 2:
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

<h2 id="c3"> Insertion Sort </h2>
{% highlight python %}
 list = [88,76,44,29,10]
 source = [92, 77, 67, 8, 6, 84, 55, 85, 43, 67]
 for index in range(1, len(source)):
 	current_val = source[index]  
 	position = index    
 	print(source)
 	while position > 0 and source[position - 1] > current_val:
 		print("Position: %s  Right side val>: %s  " % (position,source[position - 1] ))
 		source[position] = source[position - 1]  
 		position -= 1  
 	source[position] = current_val list
{% endhighlight %}

<h2 id="c4"> Shell Sort </h2>
{% highlight python %}
 import time, random
 source = [random.randrange(10 + i) for i in range(10)]
 print(source)
 interval = int(len(source) / 2)  
 print('Interval:',interval)

 while interval > 0:
 	print("---interval ---", interval)  
 	for index in range(len(source)):   
 		if index + interval < len(source):
 			current_val = source[index]
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


<h2 id="c5"> Binary search algorithm  </h2>
Method 1:
{% highlight python %}
 def tu(list,arg):
     min = 0
     max = len(list) - 1

     while min <= max:
         mid = int((min+max)/2)
         m = list[mid]

         if arg > m:
             print("Finding number %s to the right of the current median %s"%(arg,m))
             min = mid+1
         elif arg < list[mid]:
             print("Finding number %s to the left of the current median %s"%(arg,m))
             max = mid - 1
         else:
             print("Here is",mid)
             return (mid)

 if __name__ == "__main__":
     list = [2, 1, 5, 3, 7]
     list.sort()	 
     print(list)
     tu(list,6)
{% endhighlight %}

Method 2:
{% highlight python %}
 data = [1, 3, 6, 7, 9, 12, 14, 16, 17, 18, 20, 21, 22, 23, 30, 32, 33, 35]
 def binary_search(dataset,find_num):
 	print(dataset)
 	if len(dataset) >1:
 		mid = int(len(dataset)/2)
 		if dataset[mid] == find_num:  #find it
 			print("Found it",dataset[mid])

        elif dataset[mid] > find_num :# The number you are looking for is on the left of mid
 			return binary_search(dataset[0:mid], find_num)

        else:
 			return binary_search(dataset[mid+1:],find_num)

    else:
 		if dataset[0] == find_num:  #find it
 			print("Foung it",dataset[0])

        else:
 			print("The number [%s] you are looking for is not in the list" % find_num)
 binary_search(data,66)
{% endhighlight %}

<h2 > Fibonacci </h2>
{% highlight python %}
 def fab(max):
 	n, a, b = 0, 0, 1
 	while n < max:
 		print b
 		a, b = b, a + b
 		n = n + 1
{% endhighlight %}
