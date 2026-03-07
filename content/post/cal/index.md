---
title: "Cal"
description: 
date: 2024-11-07T08:19:59Z
image: 
math: 
license: 
comments: true
build:
    list: always    # Change to "never" to hide the page from the list
---

# 1 算法基础
## 1.1 二分查找
二分查找是一种算法，其输入是一个有序的元素列表，对于包含n个元素的列表。
用二分查找最多需要log2n步，而简单查找最多需要n步。
```
def binary_search(list, item): 
    low = 0 
    high = len(list)-1 
    while low <= high:
        mid = (low + high)
        guess = list[mid] 
        if guess == item:
            return mid 
        if guess > item:
            high = mid - 1 
        else:
            low = mid + 1 
            return None 

my_list = [1, 3, 5, 7, 9] 

print(binary_search(my_list, 3)) 
print(binary_search(my_list, -1)) 
```
## 1.2 大O表示法
大O表示法是一种特殊的表示法，指出了算法的速度有多快。
### 1.2.1 算法的运行时间以不同的速度增加
![alt text](b0916f3c-f78a-4107-d82d-a7c9843c4f5f-1.png)
![alt text](2bb365d3-efed-4a83-dc39-6baf00d9eee3-2-1.png)
### 1.2.2 一些常见的大O的运行时间
O(log n)，也叫对数时间，这样的算法包括二分查找。
O(n)，也叫线性时间，这样的算法包括简单查找。
O(n * log n)，这样的算法包括第4章将介绍的快速排序——一种速度较快的排序算法。
O(n2)，这样的算法包括第2章将介绍的选择排序——一种速度较慢的排序算法。
O(n!)，这样的算法包括接下来将介绍的旅行商问题的解决方案——一种非常慢的算法。<br>
算法的速度指的并非时间，而是操作数的增速。
算法运行时间并不以秒为单位。
算法运行时间是从其增速的角度度量的。
算法的运行时间用大O表示法表示。O(log n)比O(n)快，当需要搜索的元素越多时，前者比后者快得越多。
# 2 选择排序
# 2.1 内存的工作原理
需要将数据存储到内存时，你请求计算机提供存储空间，计算机给你一个存储地址。需要存储多项数据时，有两种基本方式——数组和链表。
## 2.2 数组和链表
数组的元素都在一起。
链表的元素是分开的，其中每个元素都存储了下一个元素的地址。
数组的读取速度很快。
链表的插入和删除速度很快。
在同一个数组中，所有元素的类型都必须相同（都为int、double等）。
有两种访问方式：随机访问和顺序访问。
顺序访问意味着从第一个元素开始逐个地读取元素。链表只能顺序访问
![alt text](82e92ba1-01e9-4853-9c06-581ba3fd7bca-1.png)
## 2.3 选择排序
随着排序的进行，每次需要检查的元素数在逐渐减少，最后一次需要检查的元素都只有一个。
需要的总时间为 O(n × n)，即O(n^2)。
```
def findSmallest(arr): 
    smallest = arr[0]
    smallest_index = 0 
    for i in range(1, len(arr)): 
        if arr[i] < smallest: 
            smallest = arr[i] 
            smallest_index = i 
    return smallest_index 

def selectionSort(arr):
    newArr = [] 
    for i in range(len(arr)): 
        smallest = findSmallest(arr)
        newArr.append(arr.pop(smallest)) 
    return newArr 

print(selectionSort([5, 3, 6, 2, 10]))
```
# 3 递归
递归——函数调用自己
## 3.1 基线条件和递归条件
每个递归函数都有两部分：基线条件（base case）和递归条件（recursive case）。
递归条件指的是函数调用自己，而基线条件则指的是函数不再调用自己，从而避免形成无限循环。
## 3.2 栈
栈有两种操作：压入和弹出。
### 3.2.1 调用栈
这个栈用于存储多个函数的变量，被称为调用栈。
所有函数调用都进入调用栈。
调用另一个函数时，当前函数暂停并处于未完成状态。该函数的所有变量的值都还在内存中。
调用栈可能很长，这将占用大量的内存。
### 3.2.2 递归调用栈
![alt text](13cb2a0e-4cbf-4cce-8c89-daefa4d789ea-1.png)
![alt text](8e4608ae-48b7-42ca-f946-d6f5fdc04dc6-1.png)
![alt text](8a0ab4eb-1adc-4c02-ecfc-cfc56950ce81-1.png)
![alt text](5b029480-f68d-4db5-a000-55e70ff38c93-1.png)
![alt text](1397cf89-9a0a-4973-f9a0-858ba6fdb72e-1.png)
![alt text](4308e556-8bc8-4fa9-f3af-ebde46067d7f-1.png)
每个fact调用都有自己的x变量。在一个函数调用中不能访问另一个的x变量。
# 4 快速排序
## 4.1 分而治之
使用D&C解决问题的过程包括两个步骤。
(1) 找出基线条件，这种条件必须尽可能简单。（最容易处理的情况是，一条边的长度是另一条边的整数倍。）
(2) 不断将问题分解（或者说缩小规模），直到符合基线条件。<br>
使用递归函数完成
（1）找出基线条件
（2）每次递归调用都必须离空数组更近一步
## 4.2 快速排序
快速排序也使用了D&C
基线条件为数组为空或只包含一个元素。
从数组中选择一个元素，这个元素被称为基准值（pivot）。
(1) 选择基准值。
(2) 将数组分成两个子数组：小于基准值的元素和大于基准值的元素。
(3) 对这两个子数组进行快速排序。
## 4.3 平均情况和最糟情况
在最糟情况下，栈长为O(n)，而在最佳情况下，栈长为O(log n)。
# 5 散列表(无序)
使用散列函数来确定元素的存储位置。
散列表的查找、插入和删除速度都非常快。
结果必须是均匀分布的，
## 5.1 散列函数
“将输入映射到数字”<br>
必须满足要求：
（1）它必须是一致的（总是将同样的输入映射到相同的索引）
（2）它应将不同的输入映射到不同的数字。
（3）散列函数知道数组有多大，只返回有效的索引。<br>
散列表：散列表由键和值组成。是一种结合使用散列函数和数组创建的数据结构。
## 5.2 应用案例
### 5.2.1 将散列表用于查找
提供DNS解析（将网址映射到IP地址）
### 5.2.2 防止重复
使用散列表来检查是否重复，速度非常快。
### 5.2.3 将散列表用作缓存
缓存的数据存储在散列表中<br>
缓存的工作原理：网站将数据记住，而不再重新计算。<br>
优点：
（1）用户能够更快地看到网页
（2）需要做的工作更少
```
cache = {}  

def get_data_from_server(url):  
    print(f"Fetching data from server for URL: {url}")  
    return f"Data for {url}"  

def get_page(url):  
    if url in cache:   
        return cache[url] 
    else:  
        data = get_data_from_server(url)  
        cache[url] = data 
        return data 

print(get_page("http://baidu.com"))
```
## 5.3 冲突
给两个键分配的位置相同。<br>
处理冲突的方式：如果两个键映射到了同一个位置，就在这个位置存储一个链表。<br>
要避免冲突，需要有：
(1)较低的填装因子；
(2)良好的散列函数。
## 5.4 性能
![alt text](708a4340-5af9-4ee4-9d87-26afcc179e4a-1.png)
### 5.4.1 填装因子
![alt text](47f89431-92f9-4bbc-b563-d27c7c2901da-1.png)
散列表使用数组来存储数据
填装因子度量的是散列表中有多少位置是空的<br>
调整长度：一旦填装因子开始增大（超过0.7），你就需要在散列表中添加位置
### 5.4.2 良好的散列函数
良好的散列函数让数组中的值呈均匀分布。
# 6 广度优先搜索
解决最短路径问题的算法被称为广度优先搜索。
## 6.1 图简介
(1) 使用图来建立问题模型。
(2) 使用广度优先搜索解决问题。
## 6.2 图是什么
图由节点（node）和边（edge）组成。一个节点可能与众多节点直接相连，这些节点被称为邻居。
## 6.3 广度优先搜索
广度优先搜索是一种用于图的查找算法，可解决问题：
第一类问题：从节点A出发，有前往节点B的路径吗？
第二类问题：从节点A出发，前往节点B的哪条路径最短？
### 6.3.1 查找最短路径
在广度优先搜索的执行过程中，搜索范围从起点开始逐渐向外延伸，即先检查一度关系，再检查二度关系。
### 6.3.2 队列
队列类似于栈，你不能随机地访问队列中的元素。
队列只支持两种操作：入队和出队。<br>
<font color=#DC143C>队列是一种先进先出的数据结构，而栈是一种后进先出的数据结构。</font>

## 6.4 实现图
有向图：关系是单向
无向图：没有箭头，直接相连的节点互为邻居
## 6.5 实现算法
在Python中，可使用函数deque来创建一个双端队列。
```
from collections import deque  

def person_is_seller(name):  
    return name[-1] == 'm'  

def search_queue(start):  
    search_queue = deque()  
    search_queue += graph[start]    
    while search_queue:  
        person = search_queue.popleft()  
        if person_is_seller(person):  
            print(person + " is a mango seller")  
            return True  
        else:  
            search_queue += graph[person]  
    return False  

graph = {}  
graph["you"] = ["alice", "bob", "claire"]  
graph["bob"] = ["anuj", "peggy"]  
graph["alice"] = ["peggy"]  
graph["claire"] = ["thom", "jonny"]  
graph["anuj"] = []  
graph["peggy"] = []  
graph["thom"] = []  
graph["jonny"] = []  

search_queue("you")
```
## 6.6 运行时间
广度优先搜索的运行时间为O(人数 + 边数)，这通常写作O(V + E)，其中V为顶点（vertice）数，E为边数。
# 7 狄克斯特拉算法
## 7.1 使用狄克斯特拉算法
狄克斯特拉算法包含4个步骤。
(1) 找出“最便宜”的节点，即可在最短时间内到达的节点。
(2) 更新该节点的邻居的开销，其含义将稍后介绍。
(3) 重复这个过程，直到对图中的每个节点都这样做了。
(4) 计算最终路径。
## 7.2 术语
狄克斯特拉算法用于每条边都有关联数字的图，这些数字称为权重
带权重的图称为加权图
不带权重的图称为非加权图<br>
狄克斯特拉算法只适用于有向无环图
## 7.3 负权边
如果有负权边，就不能使用狄克斯特拉算法。<br>
```
def find_lowest_cost_node(costs):
    lowest_cost = float("inf")
    lowest_cost_node = None
    for node in costs:
        cost = costs[node]
        if cost < lowest_cost and node not in processed:
            lowest_cost = cost
            lowest_cost_node = node
    return lowest_cost_node

graph={}
graph["you"]=["alice","bob","claire"]
graph["start"] = {} 
graph["start"]["a"] = 6 
graph["start"]["b"] = 2    
graph["a"] = {} 
graph["a"]["fin"] = 1 
graph["b"] = {} 
graph["b"]["a"] = 3 
graph["b"]["fin"] = 5 
graph["fin"] = {}

infinity = float("inf") 
costs = {} 
costs["a"] = 6 
costs["b"] = 2 
costs["fin"] = infinity

parents = {} 
parents["a"] = "start" 
parents["b"] = "start" 
parents["fin"] = None

processed = []

node=find_lowest_cost_node(costs)
while node is not None:
    cost=costs[node]
    neighbors=graph[node]
    for n in neighbors.keys():
        new_cost=cost+neighbors[n]
        if costs[n]>new_cost:
            costs[n]=new_cost
            parents[n]=node
    processed.append(node)
    node=find_lowest_cost_node(costs)


print("Costs:", costs)  
print("Parents:", parents)  
```
# 8 贪婪算法
优点——简单易行
每步都采取最优的做法
狄克斯特拉算法
## NP完全问题
旅行商问题和集合覆盖问题需要计算所有的解（阶乘函数），并从中选出最小/最短的那个<br>
面临NP完全问题时，最佳的做法是使用近似算法。
贪婪算法易于实现、运行速度快，是不错的近似算法。
# 9 动态规划
动态规划先解决子问题，再逐步解决大问题。
![alt text](03516fdb-75e4-42ec-c49c-852c726d37be-1.png)
![alt text](fa79b792-d201-47ba-cfa1-c0060d9e96d4-1.png)
问题可分解为离散子问题时，可使用动态规划来解决。
每种动态规划解决方案都涉及网格。
单元格中的值通常就是你要优化的值。
每个单元格都是一个子问题，因此你需要考虑如何将问题分解为子问题。
## 最长公共子序列之解决方案
![alt text](6fcaf2fc-c80a-41a0-cf94-61fef1327c5d-1.png)
```
if word_a[i] == word_b[j]:
  cell[i][j] = cell[i-1][j-1] + 1 
else: 
  cell[i][j] = max(cell[i-1][j],cell[i][j-1]) 
```
## 10 K最近邻算法
KNN用于分类和回归，需要考虑最近的邻居。
分类就是编组。
## 10.2 创建推荐系统
### 10.2.1 特征抽取
特征抽取意味着将物品（如水果或用户）转换为一系列可比较的数字。
能否挑选合适的特征事关KNN算法的成败。 
### 10.2.2 回归
回归就是预测结果（如数字）。
## 10.3 OCR
OCR指的是光学字符识别（optical character recognition）
OCR算法提取线段、点和曲线等特征。
OCR的第一步是查看大量的数字图像并提取特征，这被称为训练（training）。
# 11 算法
## 11.1 树
二叉查找树：对于其中的每个节点，左子节点的值都比它小，而右子节点的值都比它大。
二叉查找树的插入和删除操作的速度要快得多。
## 11.2 反向索引
一个散列表，将单词映射到包含它的页面。这种数据结构被称为反向索引（inverted index），常用于创建搜索引擎
## 11.3 傅里叶变换
傅里叶变换非常适合用于处理信号，可使用它来压缩音乐。
## 11.4 并行算法
改善性能和可扩展性
并行性管理开销
负载均衡
## 11.5 MapReduce
MapReduce是一种流行的分布式算法，你可通过流行的开源工具Apache Hadoop来使用它。<br>
原理：
映射函数：它接受一个数组，并对其中的每个元素执行同样的处理（映射是将一个数组转换为另一个数组。）
归并函数：将很多项归并为一项。（归并是将一个数组转换为一个元素。下面是一个示例。）
## 11.6 布隆过滤器和HyperLogLog
布隆过滤器是一种概率型数据结构
布隆过滤器的优点在于占用的存储空间很少<br>
HyperLogLog近似地计算集合中不同的元素数，而占用的内存空间却少得多。
面临海量数据且只要求答案八九不离十时，可考虑使用概率型算法！
## 11.7 SHA算法
安全散列算法函数是散列函数。给定一个字符串，SHA返回其散列值。
## 11.8 局部敏感的散列算法
对字符串做细微的修改，Simhash生成的散列值也只存在细微的差别
## 11.9 Diffie-Hellman密钥交换
Diffie-Hellman使用两个密钥：公钥和私钥。
## 11.10 线性规划
线性规划用于在给定约束条件下最大限度地改善指定的指标。
线性规划使用Simplex算法