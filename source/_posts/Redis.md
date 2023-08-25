---
title: Redis缓存
date: 2023-07-12 11:18:57
tags: 中间件
categories: 中间件
cover: https://w.wallhaven.cc/full/85/wallhaven-85gw6k.jpg 
---

# Redis

### 一、NoSQL数据库简介

#### 1.1 NoSQL数据库概述

NoSQL(Not Only SQL)不仅仅是SQL，泛指非关系性数据库，NoSQL不依赖业务逻辑方式存储，而以简单的Key-value模式存储，因此大大的增加了数据库的扩展能力

>不遵循SQL标准
>
>不支持ACID(原子性，一致性，隔离性，持久性)
>
>远超于SQL的性能

#### 1.2 NoSQL适用场景

> 对数据高并发的读写
>
> 海量数据的读写
>
> 对数据高扩展性

#### 1.3 Memcache

1. 数据都在内存中，一般不持久化
2. 支持简单的key-value模式，支持类型单一
3. 一般是作为缓存数据库辅助持久化的数据库

#### 1.4 Redis

1. 数据都在内存中，支持持久化，主要用于备份恢复
2. 除了简单的key-value模式，还支持多种数据结构的存储，比如list、set、hash、zset
3. 一般是作为缓存数据库辅助持久化的数据库

#### 1.5 MongoDB

1. 高性能、开源、模式自由(schema free)的文档型数据库
2. 数据都在内存中，如果内存不足，吧不常用的数据保存到硬盘
3. 虽然是key-value模式，但是对value(尤其是json)，提供了丰富的查询功能
4. 支持二进制数据及大型对象
5. 可以根据数据的特点代替RDBMS，成为独立的数据库，或者配合RDBMS，存储特定的数据

#### 1.6 行式存储数据库

##### 1.6.1 行式数据库

​	字段一行一行对应上面的字段存储内容

##### 1.6.2 列式数据库

​	一列一列存储数据

1. Hbase

   Hbase是Hadoop项目中的数据库，它用于需要对大量的数据进行随机、实时的读写操作场景中

   Hbase的目标就是处理数据量非常庞大的表，可以用普通的计算机处理超过10亿行数据，还可处理有数百万列元素的数据表

2. Cassandra

   Apache Cassandra是一款免费的开源NoSQL数据库，其设计目的在于管理由大量商用服务器构建起来的庞大集群上的海量数据集(数据量通常达到PB级别)在众多显著特性当中，Cassandra最为卓越的长处是对写入及读取操作进行规模调整，而且其不强调主集群的设计思路能够以相对直观的方式简化各集群的创建与扩展流程

#### 1.7 图关系型数据库

​	主要应用于：社会关系、公共交通网络、地图及网络拓扑 



### 二、Redis概述和安装

#### 1.特点

- Redis是一个开源的key-value存储系统
- 和Memcached类似，它支持存储的value类型相对更多
- 这些数据类型都支持push/pop，add/remove及取交集和差集及更丰富的操作，而且这些操作都是原子性
- Redis支持各种不同的排序
- 为了保证效率，数据都是缓存在内存中
- Redis会周期性的吧更新的数据写入到磁盘中，或者把修改操作写入追加的记录文件中
- 并且在此基础上实现了master-slave主从同步

#### 2.应用场景

配合关系型数据库做高速缓存

- 高频次，降低数据库IO
- 分布式架构，做session共享
- 缓存加速
- 排行榜场景
- 分布式计数器，锁，会话...

#### 3.安装

##### 3.1 安装C语言的编译环境

> yum install gcc
>
> gcc --version

##### 3.2 解压Redis压缩文件

> tar -zxvf redis-6.2.1.tar.gz

##### 3.3 进入Redis解压目录里面进行编译

> cd redis-6.2.1
>
> make
>
> - make报错 -Jemalloc/jemalloc,h:没有这个文件
> - 第一步：查看gcc 版本是否安装成功
> - 第二步：make distclean
> - 第三步：make 再次进行编译

##### 3.4 编译好之后进行安装

> make install

##### 3.5 查看安装目录

> cd /usr/local/bin
>
> 默认安装目录：
>
> - redis-benchmark:性能测试工具，可以在自己本子运行，看看自己本子性能如何
> - redis-check-aof:修复有问题的AOF文件
> - redis-check-dump:修复有问题的dump。rdb文件
> - redis-sentinel:Redis集群使用
> - **`redis-server`**:Redis服务器启动命令
> - **`redis-cli`**:客户端，操作入口

##### 3.6 启动Redis

###### 3.6.1 前台启动(不推荐)

​	前台启动，命令行窗口不能关闭，否则服务器停止

> cd /usr/local/bin
>
> redis-server

###### 3.6.2 后台启动(推荐)

​	后台启动，命令行窗口不显示，服务器后台运行，不会停止

1. **进入redis-6.2.1目录中**

   > cd /opt/redis-6.2.1

2. **复制redis.conf**

   > cp redis.conf /etc/redis.conf

3. **修改etc下的redis.conf的复制文件**

   > cd /etc
   >
   > vi redis.conf
   >
   > 修改`daemonize no`改为 `yes`

4. **启动**

   > cd /usr/local/bin
   >
   > redis-server /etc/redis.conf
   >
   > ps -ef | grep redis   查询是否启动

5. **客户端连接**

   > redis-cli 
   >
   > ping   测试连接

##### 3.7 关闭Redis

1. 单实例关闭：redis-cli shutdown
2. 也可以进入终端关闭：shutdown
3. 多实例关闭：redis-cli -p6379(端口号) shutdown
4. kill -9 进程号  

#### 4. Redis相关知识

​	Redis端口的由来：默认端口6379，6379对应的是按键机的Merz，Alessia `Merz`是一名演员

- Redis默认提供了`16个数据库`，类似数组下标从0开始初始`默认使用0号数据库`
- 使用命令 **`selec`** <dbid>来切换数据库。例如：select 8，切换到8号数据库
- 统一密码管理。所有库同样密码
- **`dbsize`**查看当前数据库的key的数量
- **`flushdb`**清空当前数据库
- **`flushall`**通杀全部库

​	多路复用是指使用一个线程来检查多个文件描述符(Socket)的就绪状态，比如调用select和poll函数，传入多个文件描述符，如果有一个文件描述符就绪，则返回，否则阻塞知道超时。得到就绪状态后进行真正的操作可以在同一个线程里执行，也可以启动线程执行(比如**使用线程池**)

Memcache：`多线程+锁(memcached)`

Redis：`单线程+多路IO复用`

`Redis与Memcache三点不同：支持多数据类型，支持持久化，单线程+多路IO复用`

### 三、常用的五大数据类型

#### 1.Redis键(key)

> - keys *查看当前库所有key(匹配：key *1)
> - exists key 判断某个key是否存在
> - type key 查看你的key是什么类型
> - del key 删除指定的key数据
> - `unlink key 根据value选择非阻塞删除`
> - 仅将keys从keyspace元数据中删除，真正的删除会在后续异步操作
> - expire key 10 10秒钟：为给定的key设置过期时间
> - ttl key 查看还有多少秒过期，-1表示永不过期，-2表示已过期
> - select命令切换数据库
> - dbsize查看当前数据库的key的数量
> - flushdb清空当前库
> - flushall通杀全部库

#### 2.Redis字符串(String)

##### 2.1 简介

1. String是Redis最基本的类型，你可以理解成与Memcached一模一样的类型，一个key对应一个value
2. String类型是二进制安全的，意味着Redis的String可以包含任何数据，比如jpg图片或者序列化对象
3. String类型是Redis最基本的数据类型，一个Redis中字符串value最多可以是512M

##### 2.2 常用命令

set <key><value>添加键值对

get <key>查询对应键值

append <key><value>将给定的<value>追加到原值的末尾

strlen <key>获得值的长度

setnx <key><value>只有在key不存在时 设置key的值

incr <key> 将key中存储的数字增值1

​		   只能对数字值操作，如果为空，新增值为1
