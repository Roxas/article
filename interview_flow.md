# 后端面试流程归纳总结
## 概述
  近日，在下被抽出作为面试考官对面试者进行技术面试，中间过程不再复述。但是回顾最近自己面试考官的表现，特别是经过有丰富面试经验的人指出我的问题，我发现自己在作为面试考官的时候，是有很多问题的，最大的问题，就是没有形成一套模式化，流程化，有迹可循的面试题集合，问的题有时候太跳跃，缺少引导性；在问题措辞上，有时候太官方，不够平易近人，或者不容易让人理解；没有形成记忆惯性，漏了部分问题。因此，写下这遍文章作为以后作为技术面试的一个参考。
## 面试题基本流程
### 0.看候选人简历
  大概扫一眼候选人的项目经历和技能特点，擅长什么。对候选人有个初步了解。
### 1.请面试者做基本介绍(必选)
  一般常规问题，主要看下面试者的语言表达能力，初步的沟通能力。这里要求面试官和蔼可亲，至少面带笑容，给候选人呈现出一种比较**轻松的氛围**，期待候选人不会因为太紧张，不能发挥水平。
### 2.请介绍自己最大的优点和缺点(可选)
  进一步观察候选人的沟通能力，从候选人发现闪光点。
### 3.技术面试问题(必选)
  技术面试流程，最好以时间排序，让候选人介绍最近所做项目开始
#### 3.1一般python web 框架和python app部署问题
```flow
st=>start: Start
op1=>operation: 介绍最近所做项目(这里默认使用了django/flask框架)
op2=>operation: 简述下从浏览器(req)到后端web app的resp的流程(越详细越好，开放式问题)
op3=>operation: 反向代理到后端app所用协议， WSGI协议(重点考察)，CGI协议，HTTP协议
op4=>operation: 后端使用哪种wsgi服务uWSGI/gunicorn
op5=>operation: 应用服务器如何扩容？增加进程，多加机器，数据库扩容
op6=>operation: 定时任务/长时间任务，celery相关
op7=>operation: 为什么不能使用runserver部署
e=>end
st->op1->op2->op3->op4->op5->op6->op7->e
```
#### 3.2内存缓存和数据库问题
  当候选人提到使用缓存和数据库后，面试官需要在合适时机跳转到缓存和数据库的问题上来
##### 3.2.1 redis的问题
```flow
st=>start: Start
op1=>operation: redis的常用数据类型
op2=>operation: redis数据如何持久化RDB/AOF
op3=>operation: redis的缓存策略
op4=>operation: redis的有序集合的底层实现(跳表)
op5=>operation: redis的集群搭建，redis高可用
op6=>operation: redis是否支持事务(开放式问题)
cond1=>condition: 是否需要深入redis?
e=>end
st->op1->op2->op3->cond1
cond1(yes)->op4->op5->op6->e
cond1(no)->e
```
##### 3.2.2 mysql的问题
```flow
st=>start: Start
op1=>operation: 是否熟悉SQL? where & having 区别
op2=>operation: mysql的有哪些存储引擎
op3=>operation: 事务的四个特征ACID
op4=>operation: 如何优化mysql的query,explain,slow query log
op5=>operation: 索引的问题(实现，索引类型，组合索引，聚合索引)
op6=>operation: 事务的底层实现,mysql如何做到原子性
op7=>operation: 为什么要使用外键？优点缺点
op8=>operation: mysql的高可用
cond1=>condition: 是否继续深入mysql?
e=>end
st->op1->op2->op3->op4->op5->cond1
cond1(yes)->op6->op7->op8->e
cond1(no)->e
```
##### 3.2.3 mongodb的问题
```flow
st=>start: Start
op1=>operation: 介绍下mongodb特征(开放式问题)
op2=>operation: mongodb的shard
op3=>operation: mongodb的索引
e=>end
st->op1->op2->op3->e
```
#### 3.3 一般python的问题
在问了一些比较难的问题后，可以由"刚刚咱们问了很多XXX问题，我们这里招聘的python后端开发，所以还是问一些python的相关问题"开始引导候选者进入python问题的回答, 这里笔者仅仅是做个一般例子，python还有各种问题可问，这个问题流程图，可以继续扩展。当然，面试官也可以就直接问python相关问题。
```flow
st=>start: Start
op1=>operation: 介绍常用python容器
op2=>operation: 解释迭代器,生成器
op3=>operation: 什么是上下文管理器(用过with关键字吗？with是如何实现的)
op4=>operation: 装饰器
op5=>operation: dict的底层实现
op6=>operation: 什么是协程, greenlet
op7=>operation: python的gc, gc的底层实现
op8=>operation: 协程的底层实现
op9=>operation: 介绍下更多的gc算法
op10=>operation: 什么是循环引用
op11=>operation: 介绍下__init__.py的用法
op12=>operation: 什么是循环import
op13=>operation: 元类/python C扩展/import机制
cond1=>condition: 是否深入协程
cond2=>condition: 是否深入gc
cond3=>condition: 是否深入python高阶
e=>end
st->op1->op2->op3->op4->op5->op6->cond1
cond1(yes)->op8->cond2
cond1(no)->cond2
cond2(yes)->op7->op10
cond2(no)->op10
op10->op11->op12->cond3
cond3(yes)->op13->e
cond3(no)->e
```
#### 3.4 TCP/IP，HTTP的相关问题
  很多人在工作很多年后，其实对TCP/IP协议栈已经不怎么熟悉，一般也就知道三次握手和四次握手, TCP/IP协议几层等。原则是知道这些就足够了，我们需要重点考察HTTP协议，以及相关的东东JSON/XML，websocket， RESTFUL，RPC问题。
```flow
st=>start: Start
op1=>operation: 介绍下HTTP的方法(GET，PUT，POST，DELETE等)
op2=>operation: 经典问题GET， POST区别
op3=>operation: json和xml相比有什么优点
op4=>operation: 如何主动push消息到浏览器
op5=>operation: RESTFUL API设计的特点
op6=>operation: API设计问题，删除对象API的设计
cond1=>condition: 用过XML吗？
e=>end
st->op1->op2->cond1
cond1(yes)->op3->op4
cond1(no)->op4
op4->op5->op6->e
```
#### 3.5 linux/BSD，docker问题
   因为后台工作的关系，这里需要候选人对linux下工作方法很熟悉，尝试问一点linux的基本概念性问题；同时因为采用docker来跑本地服务，需要候选人能熟练使用docker。
```flow
st=>start: Start
op1=>operation: 解释进程，线程，僵尸进程，守护进程
op2=>operation: 如何查看进程
op3=>operation: 如何查看网络服务/端口占用
op4=>operation: 如何进入docker bash环境
op5=>operation: docker的底层实现cgroup, namespace隔离等
op6=>operation: k8s/swarm容器编排问题
cond1=>condition: 是否深入docker
e=>end
st->op1->op2->op3->op4->cond1
cond1(yes)->op5->op6->e
cond1(no)->e
```
#### 3.6 一些开放式技术问题
这里有部分开放式问题，在可以在合适时机提出。什么是合适时机，需要在和候选人的问答互动中，择机提出，主要查看候选人这部分开发技能的经验积累。
```flow
st=>start: Start
sub1=>subroutine: 根据时机情况选择问题
op1=>operation: 如何处理日志，清理日志。
op2=>operation: 浏览器发起请求后，不能正确得到服务器相应结果,如何debug
op3=>operation: 现在发现某段python代码允许很慢，已经知道这段代码主要消耗的资源是CPU，如何优化
op4=>operation: 什么是IO多路复用，epoll，边缘触发/水平触发。
e=>end
st->op1->op2->op3->op4->e
```
### 4.请介绍自己满意的项目(可选)
  如果在前面技术面试中，可能因为候选人的项目经历和面试官本身熟悉的技术领域和方向不太匹配，这里可以给候选人一个机会，来弥补介绍自己的机会
### 5.询问面试者有什么问题(必选)
  作为面试快结束的标志。这里需要注意，不要随便对候选人做什么承诺，或者对公司不切实际的描述，不涉密。
## 结语
  面试是对公司或者对个人都是一件严肃的问题，公司需要招聘到靠谱的人，个人希望进入靠谱的公司，为了提升自己作为面试官的水平，特写此文章。我这里面试流程仅仅针对中级以上后台开发而言。对于应届生，该流程不太合适，有机会会再出一个适合应届生的面试官问题流程。这里归纳总结的问题，还有很多可以优化的地方，在下会继续归纳优化。