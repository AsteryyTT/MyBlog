---
title: JDBC
date: 2023-02-02 11:18:57
tags: Java
categories: Java
cover: https://w.wallhaven.cc/full/85/wallhaven-85gw6k.jpg 
---

# JDBC

### 一、什么是JDBC

​	Java DataBase Connectivity

​	使用Java代码连接并操作数据库的一套技术解决方案

### 二、JDBC工作原理

1. JDBC技术标准制定了一套标准接口，在接口中定义了访问并操作数据库的通用API，这些API主要包括连接数据库，以及对数据表进行增删改查等抽象方法

2. 各大数据库厂商根据JDBC技术标准制定的接口来编写实现类，实现接口中各种抽象方法，并将接口和这些实现类封装成一个jar包，提供给程序开发人员使用

3. 开发人员将jar包下载下来，导入到自己的项目中，在项目中使用接口来实现对数据库的访问操作，根据java多态底层会自动去调用实现类中重写的方法

   使用接口编程的好处：

   可以让java以相同的方式去访问不同的数据库，以实现与具体数据库无关的java操作

### 三、JDBC使用步骤

1. 反射加载mysql驱动

   > Class.forName("com.mysql.cj.jdbc.Driver")

2. 连接数据库(指定IP、端口号、账号、密码、库名)

   > Connection conn = DriverManager.getConnection(url,username,passsword)

3. 预编译sql语句(创建一个sql语句对象)

   > PreparedStatement ps = conn.prepareStatement(sql)

4. 执行sql语句

   > 如果sql语句有？占位符，需要给占位符赋值
   >
   > ps.setXXX(占位符顺序，给占位符赋值)
   >
   > 执行DML语句，返回受影响的行数
   >
   > int rows = ps.executeUpdate();
   >
   > 执行DQL查询语句，返回ResultSet结果集
   >
   > ResultSet rs = ps.executeQuery();
   >
   > 结果集处理：
   >
   > rs.next()      判断结果集有没有下一行数据
   >
   > rs.getXXX(列名/列序号)    根据列名或者列序号取值，如果有别名，查询取别名

5. 释放资源

   后打开的先释放

### 四、Statement和PreparedStatement有什么区别

1. Statement 先以字符串方式拼接sql语句，然后再编译执行，又可能会造成sql注入
2. PreparedStatement 是先预编译sql语句，预编译时允许sql带上？占位符，然后再给占位符赋值，给占位符赋的值都会加上双引号，所以不会造成sql语句注入
3. PreparedStatement只需要编译一次，而Statement每次执行都需要编译，因此PreparedStatement效率更高 
4. 如果传入的是数据库对象，如列名、表名、库名时必须使用Statement，如果传入耳朵是表中的数据，建议都采用PreparedStatement

### 五、JDBC执行事务

​	通过Connection连接数据库来控制事务

1. setAutoCommit(false);     开启事务
2. commit();                 提交事务
3. rollback();               回滚事务

### 六、JDBC获取数据库元数据

​	元数据：用于描述数据的数据，例如xml和json

```json
{
	"id":1,
	"name":"张三"
}
```

1. 数据库元数据

   DatabaseMetaData data = conn.getMetaData();

2. 表元数据

   ResultSetMetaData rsd = rs.getMetaData();

