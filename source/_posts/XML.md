---
title: XML学习
date: 2022-08-18 11:18:57
tags: 计算机学习
categories: 计算机学习
cover: https://w.wallhaven.cc/full/85/wallhaven-85gw6k.jpg 
---

# XML

### 一、xml概念

​	Extensiable Markup Language

​	可扩展标记语言

### 二、 xml作用

1. 数据传输格式(由json代替了)

2. 数据库存储格式

3. 应用程序的配置文件(主要用于web开发,以及框架的配置)

   配置文件常格式: .xml .properties  .yml

### 三、xml基本组成部分

1. 声明

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   ```

   指定版本号和编码方式

2. DTD约束/Schemal约束

   主要用于约束当前xml文件应该写那些标签，
   以及这些标签所出现的先后顺序和嵌套顺序如果没有DTD约束，
   那么标签可以自己随便写

3. 标签Tag(一对尖括号)

4. 元素Element

   元素=开始标签+中间内容+结束标签

5. 根元素RootElement

   一篇xml文档有且仅有一个根元素

6. 属性Attribute

   以key=value形式出现在开始标签中

   属性值必须带上单引号或者双引号

7. 节点Node

8. 文档对象

### 四、xml解析

从xml文件提取出我们想要的数据

1. dom解析               
2. dom4解析
3. pull解析
4. sax解析