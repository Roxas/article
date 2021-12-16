# 后端面试流程归纳总结
## 概述
  面试是一件非常严肃的事情，几方面来说:1.为公司选择能力满足公司开发需求的人，这是为公司负责; 2.出来找工作都不容易，面试者也希望进入一家好的公司，能和公司一起成长，这是为被面试者负责；3.如果招聘进来的人，最后发现因为面试比较水，而蒙混过关，在实际工作合作的时候，会发现非常痛苦，这是为自己负责。以下是在下归纳总结的面试流程和面试问题。问题和流程都会根据实际情况持续更新，最后还准备加入打分项，对每个问题做到权重有多少分，能给到公司，被面试者一个可量化评估的分数。
## 面试题基本流程
### 0.看候选人简历
  大概扫一眼候选人的项目经历和技能特点，擅长什么。对候选人有个初步了解。
### 1.请面试者做基本介绍(必选)
  一般常规问题，主要看下面试者的语言表达能力，初步的沟通能力。这里要求面试官和蔼可亲，至少面带笑容，给候选人呈现出一种比较**轻松的氛围**，期待候选人不会因为太紧张，不能发挥水平。
### 2.热场
  一般来说，通过做自我介绍，如果面试者比较紧张，可能自我介绍很短，为了能让面试者更能发挥自己水平，回归平常心，需要一些问题，需要一些问题激发他的表达欲
  2.1 询问面试者自己的优点和缺点
  2.2 询问面试者有没有获得过个人奖项
### 3.技术面试问题(必选)
  技术面试流程，最好以时间排序，让候选人介绍最近所做项目开始
#### 3.1一般python web 框架和python app部署问题
```flow
st=>start: Start
op1=>operation: 介绍最近所做项目(从功能，模块，职责角色出发)
op2=>operation: 简述下从浏览器(req)到后端web app的resp的流程(越详细越好，开放式问题)
op3=>opeartion: 什么是cookie/什么是session
op4=>operation: 反向代理到后端app所用协议(wsgi,asgi,http,https)
op5=>operation: 后端使用哪种wsgi服务uWSGI/gunicorn
op6=>operation: 定时任务/长时间任务，celery相关
e=>end
st->op1->op2->op3->op4->op5->op6-op7->e
```
#### 3.2内存缓存和数据库问题
  当候选人提到使用缓存和数据库后，面试官需要在合适时机跳转到缓存和数据库的问题上来
##### 3.2.1 redis的问题
```flow
st=>start: Start
op1=>operation: redis的常用数据类型(string/hash/list/set&zset/, 特殊场景使用bitmaps/Hyperloglogs/Steams/geospatial)
op2=>operation: redis数据如何持久化(RDB数据快照/AOF文件追加/混合模式，结合RDB,AOF优点)
op3=>operation: redis的缓存策略(noeviction/allkeys-lru/volatile-lru/allkeys-random/volatile-random/volatile-ttl)
op4=>operation: redis的有序集合的底层实现(跳表skiplist)
op5=>operation: redis的集群搭建(三种方式:主从,哨兵,cluster, etc非官方)
op6=>operation: redis是否支持事务(开放式问题multi)
op7=>operation: 深入问题使用过分布式锁吗？(redis实现之单节点实现，多节点实现redlock)
cond1=>condition: 是否需要深入redis?
e=>end
st->op1->op2->op3->cond1
cond1(yes)->op4->op5->op6->op7->e
cond1(no)->e
```
##### 3.2.2 mysql的问题
```flow
st=>start: Start
op1=>operation: 是否熟悉SQL? where & having 区别
op2=>operation: mysql的有哪些存储引擎
op3=>operation: 事务的四个特征ACID(redo log, undo log, 读写锁，MVVC)
op4=>operation: 如何优化mysql的query,explain,slow query log
op5=>operation: 索引的问题(实现，索引类型，组合索引，覆盖索引)
op6=>operation: 事务的底层实现,mysql如何做到原子性(undo log)
op7=>operation: innodb底层实现，什么是回表(B+树，B树，hash索引)
op8=>operation: 列举索引失效的原因
op9=>operation: 事务的隔离级别(Read uncommitted, Read committed, Repeatable read, :Serializable)
cond1=>condition: 是否继续深入mysql?
e=>end
st->op1->op2->op3->op4->op5->cond1
cond1(yes)->op6->op7->op8->op9->e
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
op1=>operation: 介绍python内置内置类型(可变，不可变)
op2=>operation: 解释迭代器,生成器
op3=>operation: 什么是上下文管理器(用过with关键字吗？with是如何实现的)
op4=>operation: 装饰器
op5=>operation: dict的底层实现
op6=>operation: 什么是协程, greenlet
op7=>operation: python的gc, gc的底层实现
op8=>operation: 协程的底层实现
op9=>operation: 介绍下更多的gc算法
op10=>operation: 什么是循环引用/深拷贝，浅拷贝
op11=>operation: 介绍下__init__.py的用法
op12=>operation: python生成一个字符串有多少种方式，a + b + c有什么缺点
op13=>operation: 元类/python C扩展/import机制
op14=>operation: 如何对python代码做性能优化cprofile, line_profiler, memory_profiler，找出瓶颈
op15=>operation: PEP8，python代码美化
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
cond3(yes)->op13->op14->op15->e
cond3(no)->e
```
#### 3.4 Celery/rabbitmq队列问题
  Celery作为python编写的一个简单、灵活且可靠、处理大量消息的分布式系统，在处理耗时很长的阻塞任务，几乎是python唯一选择。而后端的broker可以采用RabbitMQ，redis，mq等各种消息队列实现。
```flow
st=>start: Start
op1=>operation: 有没有用过消息队列，为什么要使用消息队列。
op2=>operation: 介绍下实际生产环境你们如何使用Celery
op3=>operation: 如何处理超时任务(task_soft_time_limit),为什么建议设置这个参数,如何任务超时，怎么办。
op4=>operation: 如何处理Celery可能的内存泄漏，或者你代码产生的内存泄漏(worker_max_tasks_per_child)
e=>end
st->op1->op2->op3->op4->e
```
#### 3.5 网络相关的问题
##### 3.5.1 TCP/IP相关问题
  TCP/IP的问题一般比较基础，任何一本TCP/IP书籍都有详细阐述，但是因为工作原因，部分开发可能忘记了部分细节，我觉得这是可以理解的，但是面试者既然知道来面试，我觉得花点时间把这部分知识重新看一下，并不过分。
```flow
st=>start: Start
op1=>operation: 介绍下TCP/IP协议分为几层，每层分别是什么，什么是ISO网络模型
op2=>operation: 详细描述下TCP/IP三次握手。
op3=>operation: 为什么TCP/IP建立连接需要三次握手，为什么不能是2次？
op4=>operation: 详细介绍下TCP/IP四次握手，什么是TIME_WAIT状态
cond1=>condition: 是否深入？
e=>end
st->op1->op2->cond1
cond1(yes)->op3
cond1(no)->op4
op3->op4->e
```
##### 3.5.2 HTTP相关问题
  HTTP协议是我们重点考察的部分，以及相关的东东JSON/XML，websocket， RESTFUL，RPC问题。
```flow
st=>start: Start
op1=>operation: 介绍下HTTP的方法(GET，PUT，POST，PATCH，DELETE等)
op2=>operation: 列举你所知道HTTP Status Code/列举HTTP header字段
op3=>operation: 经典问题GET， POST区别
op4=>operation: GET请求可以携带HTTP BODY吗？
op5=>operation: 什么是JWT，JWT的使用场景，JWT特点
op6=>operation: 如何主动push消息到浏览器
op7=>operation: RESTFUL API设计的特点(URI,资源，请求，无状态)
op8=>operation: 还了解其他WebService API风格吗？说明优缺点，为什么这么设计(开放式问题graphQL, websocket)
e=>end
st->op1->op2->op3->op4->op5->op6->op7->op8->e
```
#### 3.6 linux/BSD，docker问题
   因为后台工作的关系，这里需要候选人对linux下工作方法很熟悉，尝试问一点linux的基本概念性问题；同时因为采用docker来跑本地服务，需要候选人能熟练使用docker。
```flow
st=>start: Start
op1=>operation: 解释进程，线程，僵尸进程，守护进程
op2=>operation: 如何查看进程
op3=>operation: 如何查看网络服务/端口占用
op4=>operation: 如何进入docker bash环境(docker run -it container_id/container_name) sh/bash
op5=>operation: 如何通过docker命令查看日志 docker logs -f container_id
op6=>operation: docker的底层实现cgroup, namespace隔离等
op7=>operation: 如何优化docker镜像打包(打包时间，镜像大小)
op8=>operation: k8s/swarm容器编排工具用来做什么的
cond1=>condition: 是否深入docker
e=>end
st->op1->op2->op3->op4->op5->cond1
cond1(yes)->op6->op7->op8->e
cond1(no)->e
```
#### 3.6 一些开放式技术问题
这里有部分开放式问题，在可以在合适时机提出。什么是合适时机，需要在和候选人的问答互动中，择机提出，主要查看候选人这部分开发技能的经验积累。
```flow
st=>start: Start
sub1=>subroutine: 根据时机情况选择问题
op1=>operation: 如何处理日志，清理日志(ETL)。
op2=>operation: 浏览器发起请求后，不能正确得到服务器相应结果,如何debug
op3=>operation: 现在发现某段python代码运行很慢，已经知道这段代码主要消耗的资源是CPU，如何优化
op4=>operation: 什么是IO多路复用，epoll，边缘触发ET/水平触发LT。
e=>end
st->op1->op2->op3->op4->e
```
### 4.请介绍自己满意的项目(可选)
  如果在前面技术面试中，可能因为候选人的项目经历和面试官本身熟悉的技术领域和方向不太匹配，这里可以给候选人一个机会，来弥补介绍自己的机会
### 5.询问面试者有什么问题(必选)
  作为面试快结束的标志。这里需要注意，不要随便对候选人做什么承诺，或者对公司不切实际的描述，不涉密。
## 结语
  面试是对公司或者对个人都是一件严肃的问题，公司需要招聘到靠谱的人，个人希望进入靠谱的公司，为了提升自己作为面试官的水平，特写此文章。我这里面试流程仅仅针对中级以上后台开发而言。对于应届生，该流程不太合适，有机会会再出一个适合应届生的面试官问题流程。这里归纳总结的问题，还有很多可以优化的地方，在下会继续归纳优化。