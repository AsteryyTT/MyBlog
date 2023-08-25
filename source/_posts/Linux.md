---
title: Linux安装各种软件
date: 2023-04-18 11:18:57
tags: Linux
categories: Linux
cover: /img/images/linux.jpg
---

## Linux安装MySQL

> [MySQL](https://baike.baidu.com/item/MySQL/471251?fr=aladdin)是一种[关系型数据库](https://so.csdn.net/so/search?q=关系型数据库&spm=1001.2101.3001.7020)管理系统，

**一、安装**

1. 创建一个文件夹

   > mkdir /soft/mysql
   >
   > cd /soft/mysql

2. 解压mysql安装包

   > mkdir mysql-8.0.30
   >
   > tar -xvf mysql-8.0.30-1.el7.....tar -C mysql-8.0.30

3. rpm安装

   > 切换到解压的mysql目录下，这些文件都是rpm安装包，需要下载openssl-devel插件
   >
   > yum install openssl-devel

4. 安装rpm包

   > 注意：一定要按照顺序
   >
   > rpm -ivh mysql-community-common-8.0.26-1.el7.x86_64.rpm 
   >
   > rpm -ivh mysql-community-client-plugins-8.0.26-1.el7.x86_64.rpm 
   >
   > rpm -ivh mysql-community-libs-8.0.26-1.el7.x86_64.rpm
   >
   > rpm -ivh mysql-community-libs-compat-8.0.26-1.el7.x86_64.rpm
   >
   > rpm -ivh  mysql-community-devel-8.0.26-1.el7.x86_64.rpm
   >
   > rpm -ivh mysql-community-client-8.0.26-1.el7.x86_64.rpm
   >
   > rpm -ivh  mysql-community-server-8.0.26-1.el7.x86_64.rpm
   > 
  
   > 注意：在 Linux 中 MySQL 安装好了之后系统会自动的注册一个服务，服务名称叫做mysqld，所以可以通过以下命令操作 MySQL：
   > 
   > 启动 MySQL 服务：systemctl start mysqld
   >
   > 重启 MySQL 服务：systemctl restart mysqld
   >
   > 关闭 MySQL 服务：systemctl stop mysqld

5. 启动mysql服务 

   > systemctl start mysqld

5. 查看随机密码

   > cat /var/log/mysqld.log

5. 链接数据库

   > mysql -u root -p

**二、卸载**

1. 停止mysql服务

   > systemctl stop mysqld

2. 查询Mysql的安装文件

   > rpm -qa | grep -i mysql

3. 跟据上述查询结果一条一条卸载所有Mysql安装包

   > rpm -e 文件名 --nodeps

4. 删除MySQL的数据存放目录

   >  rm -rf /var/lib/mysql/

5. 删除MySQL的配置文件备份

   > rm -rf /etc/my.cnf.rpmsave

**三、常用设置**

1. 修改用户名密码

   > ALTER  USER  'root'@'localhost'  IDENTIFIED BY '你的密码'

2. 创建用户与权限分配

   默认的 root 用户只能当前节点localhost访问，是无法远程访问的，我们还需要创建一个新的账户，用于远程访问

   > create user '用户名称'@'%' IDENTIFIED WITH mysql_native_password BY '密码';

   > 创建完用户分配所有权限
   >
   > grant all on *.* to 'mike'@'%';

3. 密码策略修改

   1. 查询Mysql所有的密码相关策略

      > show variables like 'validate_password%';

   2. 变量名说明

      >变量名说明：
      >
      >validate_password_check_user_name：关闭用户名验证，OFF/ON：关闭/开启，默认为OFF，即关闭；
      >
      >
      >validate_password_dictionary_file：用于验证密码强度的字典文件路径；
      >
      >
      >validate_password_length：密码最小长度，参数默认为8，它有最小值的限制，最小值为的计算方法为validate_password_number_count+validate_password_special_char_count +(2*validate_password_mixed_case_count) ，这就是为什么很多人搞不懂明明设置了长度为1，但是最后还是为4个的长度，所以这儿要注意；
      >
      >
      >validate_password_mixed_case_count：密码至少要包含的小写字母个数和大写字母个数，默认为1个；
      >
      >
      >validate_password_number_count：密码至少要包含的数字个数，默认为1个；
      >
      >
      >validate_password_policy：密码强度检查等级，等级总共为：0/LOW、1/MEDIUM、2/STRONG 三种，默认为MEDIUM，即1，所以刚开始设置的密码必须符合长度，且必须含有数字，小写或大写字母，特殊字符；
      >
      >validate_password_special_char_count：密码至少要包含的特殊字符数，默认为1个；

   3. 修改密码策略

      先将密码策略修改为LOW

      > set global validate_password_policy=0;

      再将密码长度修改4位

      >  set global validate_password_length=4;



## Linux安装Java

**一、安装**

1. 创建java目录

   > mkdir /usr/local/java/

2. 解压下载好的Java安装包

   > tar -zxvf jdk-8u341-linux-x64.tar.gz -C /usr/local/java/

3. 配置环境变量

   > vi /etc/profile

   > export JAVA_HOME=/usr/local/java/jdk1.8.0_341
   > export JRE_HOME=${JAVA_HOME}/jre
   > export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib
   > export PATH=${JAVA_HOME}/bin:$PATH
   
4. 使环境变量生效

   > source /etc/profile

5. 添加软链接

   > ln -s /usr/local/java/jdk1.8.0_341/bin/java /usr/bin/java

6. 查看Java版本

   > java -version

**二、卸载**

1. 查询java是否安装

   > **rpm -qa |grep java**

2. VM中查询Java列表

   > rpm -qa | grep java

3. 卸载Java

   > yum remove openjdk

4. 其他卸载命令

   >rpm -e rpm -qa|gerp java 卸载
   >
   >rpm -e rpm -qa|gerp java --nodeps 强制卸载



## Linux配置Tomcat

**一、安装**

1. 创建目录

   > mkdir /usr/local/Tomcat

2. 解压

   > tar -zxvf Tomcat安装包

3. 进入Tomcat的bin目录

   > chmod 777 ./*  给文件提高至最高权限

4. 配置环境变量

   > vim /etc/profile 

   >\#tomcat
   >export TOMCAT_HOME=/usr/local/tomcat/apache-tomcat-9.0.65
   >
   >export CATALINA_HOME=/usr/local/tomcat/apache-tomcat-9.0.65

5. 修改Tomcat启动分析脚本

   > vim /usr/local/tomcat/apache-tomcat/bin/setclasspath.sh

   > export JAVA_HOME=/usr/local/java/jdk1.8.0_341
   >
   > export JRE_HOME=${JAVA_HOME}/jre

6. 启动Tomcat

   > 进入Tomcat/bin目录
   >
   > ./startup.sh





## Linux配置Node

一、安装

1. 创建目录

   > mkdir /usr/local/node

2. 解压Node安装包

   > *tar* -xvf node-v16.15.0-linux-x64.tar.xz

3. 配置环境变量

   >*sudo v*im /etc/profile

   > export NODE_HOME=/usr/local/nodejs 
   > export PATH=$NODE_HOME/bin:$PATH

4. 让环境变量生效

   > *source* /etc/profile

5. 测试是否安装成功

   > node -v



## Linux安装Notepadqq

**一、安装**

> sudo add-apt-repository ppa:notepadqq-team/notepadqq 
>
> sudo apt-get update 
>
> sudo apt-get install notepadqq

**二、卸载**

> sudo apt-get remove notepadqq 
>
> sudo add-apt-repository --remove ppa:notepadqq-team/notepadqq
