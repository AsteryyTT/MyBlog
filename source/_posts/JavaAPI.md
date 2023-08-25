---
title: Java常用API
date: 2023-01-06 11:18:57
tags: Java
categories: Java
cover: /img/images/computer.jpg
---

# API

application programming interface

应用程序编程接口，学习Java官方已经定义好一些类或接口

1. 如何学习一个API

   1. 这个类是什么
   2. 这个类有什么作用
   3. 如何创建这个类的对象
   4. 这个类中有那些属性和方法，各有什么作用
   5. 在那些情形下需要使用到这个类

2. 怎样学好API

   1. 查手册
   2. 看源码

3. 核心API

   1. Object
   2. 字符序列:String/StringBuilder/StringBuffer
   3. 包装类:Byte/Short/Integer/Long/Float/Double/Boolean/Character
   4. 时间日期：Data/Calendar/LocalDate/LocalTime/LocatDateTime
   5. 集合容器：Collection/Map
   6. 数据结构：数组/链表/树/栈/队列/哈希表/图/堆
   7. 异常：Exception/Error
   8. 文件：File/
   9. IO流：字节流/字符流/输入/输出
   10. 线程：Thread
   11. 网络编程：Socket

4. **Java.lang包**

   语言包，使用该包下面的类都不需要导包

   1. **Java.long.Object**

      所有类的顶层父类，如果一个类没有继承其他类，默认会继承Object类，给所有的类提供一些公共的方法，让子类去继承或重写

   2. **toString**

      在Object类中该方法用于打印输出内存的哈希值，所有子类需要进行重写，重写之后可以显示对象的成员变量信息

      在默认输出对象时，默认会调用对象的toString()方法

      对象和字符串之间使用+做拼接时，默认会去调用对象的toString方法

   3. **equals**

      在Object类中,该方法默认比较的是两个对象的地址值，所有子类都需要进行重写，从而比骄两个对象的属性值，当属性值相同时就返回true，不同返回false

      是为了让子类去重写后能够比较两个类的成员变量信息，如果两个对象的属性值相同，不管是否为同一对象，那么都因该返会true否则如果属性值不同就返回false，所以我们所有的子类都需要按照规定重写

      > 面试题：equals和= = 有什么区别
      >
      > 1. 八种基本类型之间的比较都用 = =
      > 2. 引用类型之间比较地址值，也就是判断两个对象是否为同一对象，也使用 = =
      > 3. 判断一个对象是否为null，使用= = 或者！=
      > 4. 在经过子类重写后equals（）可以用于判断两个对象的属性值是否相同，如果没有重写equals（）默认比较的还是地址值
      
   4. **Java.long.String**
   
      1. 字符串，用于表示一串字，字符串的本质是一个字符数组char[]
   
      2. 字符串特点：字符串是一个常量，一旦定义后内容（长度，大小）不可改变，如果字符串内容变了，一定是创建新的字符串对象
   
         原因：字符串采用的是char[]数组做存储而char[]数组使用了private final修饰符，并且没有对外界提供公开的方法来修改这个数组
   
      3. 字符串对象的创建
   
         1. 使用构造方法来创建对象
   
            当使用构造方法创建对象时，首先会去检查字符串常量池中是否有这个字符串对象，如果没有就先在池中创建一个，然后复制到堆中，如果池中有这个对象，就就直接将池中这个对象复制到堆中，然后栈中的引用指向堆中的对象
   
         2. 利用字符串拼接创建对象
   
            字符串和其他任意类型之间可以使用+做拼接，拼接完成后的结果还是一个字符串
   
            > Java编译器优化：
            >
            > 如果右侧参与拼接的全部都是字符串字面值，在编译期间就已经确定他的结果，那么Java编译器在编译期间直接将右侧字符串字面值全部拼接在一起，然后再运行
   
            ```java
            String s1 = "abc";
            String s2 = "ab";
            String s3 = s2+"c";
            System.out,println(s1.equals(s3));//true
            System.out,println(s1==s3);//false
            ```
   
            在编译期间无法确定值是什么，只有到运行期间才会将值从变量中取出来，再参与拼接
   
      4. 字符串常量池
   
         当使用字符串字面值创建对象时，首先会去检查字符串常量池中有没有这个字符串对象，如果没有就在池中创建一个，然后栈中引用指向池中这个对象，如果有就不在创建，从而让对象可以得到复用，节省内存空间
         
      5. 字符串转为数组
      
         ```java
         String s1 = "abc";
         s1.toCharArray();
         ```
      
      6. 字符串获取长度
      
         ```java
         String s1 = "abc";
         s1.charAt(index);
         ```
      
      7. 字符串转大小写
      
         ```java
         String s1 = "abc";
         s1.toUpperCase();//大写
         s1.toLowerCase()//小写
         ```
      
      8. 判断字符串是否以指定的子串开头
      
         ```java
         String s1 = "abc";
         boolean f1 = s1.startWith("a");
         System.out.println(f1?"是指定子串开头":"不是子串开头");
         ```
      
      9. 判断字符串是否指定的子串结尾
      
         ```java
         String s1 = "刘亦菲.jpg";
         boolean f2 = s1.endsWith(.jpg);
         System.out.println(f2?"s是指定的子串结尾":"不是指定的子串结尾")
         ```
      
      10. 获取指定子串的下标位置
      
         获取第一次出现的下标位置
      
         ```java
         String s1 = "adwadjjj jwdjwad";
         int index = s1.indexOf("wa");
         ```
      
         从指定下标位置开始向后查找
      
         ```java
         String s1 = "asdfghjklqwertyu";
         int index = s1.indexOf("er",3);
         ```
      
         获取指定子串最后一次出现的下标位置
      
         ```java
         String s1 = "qwetyuiop";
         int index = s1.lastIndexOf("i");
         ```
      
         截取子串
      
         从指定下标位置开始，截取到字符串的末尾
      
         ```java
         String s1 = "zxcvbnm";
         s1.substring(12);
         ```
      
         包含开始下标位置，不包含结束下标位置
      
         截取范围[start,end]
      
         ```java
         String s1 = "qwertyuio";
         s1.substring(4,8)；
         ----------------------
         s1.substring(s1.indexOf("q"),s1.indexOf("y")+1)；
         ```
      
      11. 判断是否包含指定子串
      
          ```java
          String s1 = "qwertyuiop";
          boolean f1 = s1.contains("yui");
          System.out.println(f4?"包含:不包含")
          ```
      
      12. 判断是否为空字符串
      
          ```java
          String s1 = "";
          boolean f1 = s1.isEmpty();
          System.out.println(f1?"空字符串":"非空字符串")；
          ```
      
      13. 去除字符串两侧空白
      
          ```java
          String s1 = "  张三  "；
          s1.trim();
          ```
      
      14. 忽略大小写比较
      
          ```java
          String s1 = "asdfhg";
          String s2 ="ASDHG";
          S1.equalslgnoreCase(s2);
          ```
      
      15. 字符串以分隔符拼接
      
          ```java
          String s1 = String.join(":","张三"，"李四","王五")；
          System.out.println(s1);
          ```
      
      16. 将其他任意类型转为字符串类型
      
          基本类型
      
          ```java
          int n = 100;
          String s1 = String.valueOf(n);
          ```
      
          引用类型
      
          ```Java
          Point point = new Point(5,10);
          String s1 = String.valueOf(point);
          ```
      
   5. **正则表达式Regex**
   
      用于检测字符串是否符合某一特定规则的格式匹配工具，在Java中正则表达式是使用字符串来表示的
   
      点：任意一个字符
   
      \d：任意一个数字字符
   
      \w：任意一个单词字符（字母数字下滑线）
   
      \s：空白（空白，换行）
   
      \D：非数字字符
   
      \W：非单词字符
   
      \S：非空白字符
   
      \[0~9]:任意一个数字字符
   
      \[0~9a-zA-Z_]:任意一个数字字母下划线
   
      \[abc]:a或b或c中任意一个字符
   
      \[^abc]:排除abc外的其他任意字符
   
      ? :0个或1个
   
      \+ :一个或多个
   
      \* :0个或多个
   
      \d{6}：必须是6位数字
   
      \d{6，8}：最少6位，最多8位纯数字
   
      \d{6,}:最少6位，最多不限
   
      \d{，8}:最少不限，最多8位
   
      & ：并且
   
      | ：或者
   
   6. **字符串中跟正则表达式相关的3个API方法：**
   
      1. matches（string regex)
   
         检测字符串是否符号正则表达式制定的规则
   
      2. split(String regex)
   
         按照正则表达式的规则切分字符串
   
      3. replaceAll（String regex)
   
         按照正则表达式的规则替换字符串中能匹配上的子串
   
5. **java.lang.StringBuilder**

   1. 可变字符串，比较适合用于对字符串进行频繁修改，无论修改（内容，大小，长度）多少次，都是原来那个对象，不会创建新的对象，其内部维护着一个可变的字符数组，节省系统内存

   2. 构建对象

      ```java
      //无参构造默认初始化另一个16长度的字符数组
      StringBuilder sb = new StringBuilder()；
      //带参数构造方法，参数长度+16的长度数组
      StringBuilder sb = new StringBuilder("abc")；
      ```

   3. apped()追加内容

      ```java
      StringBuilder sb = new StringBuilder("abc")；
      sb.apped("def");
      System.out.println(sb);
      ```

   4. reverse()内容翻转

   5. setCharAt(0,0)替换指定下标位置字符

   6. delete(0,0)删除指定下标位置包前不包后[start,end)

6. **java.lang.StringBuffer**

   











