---
title: Spring框架
date: 2023-03-12 11:18:57
tags: 框架
categories: 框架
cover: /img/images/Spring.jpg
---


## 一、spring框架概述

1. Spring是轻量级的开源的JavaEE框架
2. Spring可以解决企业应用开发的复杂性
3. Spring有两个核心部分：IOC和AOP
   1. IOC：控制反转，把创建对象过程交给Spring进行管理
   2. AOP：面向切面，不修改源代码进行功能增强
4. Spring特点
   1. 方便解耦，简化开发
   2. AOP编程支持
   3. 方便程序测试
   4. 方便和其他框架进行整合
   5. 方便进行事务操作
   6. 降低API开发难度

## 二、IOC

### IOC(概念和原理)

##### 一、什么是IOC

1. 控制反转，把对象创建和对象之间的调用过程，交给Spring进行管理
2. 使用IOC的目的：为了降低程序的耦合度
3. Spring容器在初始化时先读取配置文件，根据配置文件或元数据，创建并组织对象存入容器中，程序使用时再从Ioc容器中取出需要的对象。

##### 二、IOC底层原理

1. XML解析、工厂模式、反射

2. 原始方式创建

   ```java
   class UserService{
   	execute(){
   		UserDao dao = new UserDao();
   		dao.add();
   	}
   }
   class UserDao{
       add(){
           ......
       }
   }
   ```

3. 工厂模式

   ```java
   class UserService{
       execute(){
           UserDao dao = UserFactory.getDao();
           dao.add();
       }
   }
   class UserDao{
       add(){
           ......
       }
   }
   class UserFactory{
       public static UserDao getDao(){
           return new UserDao();
       }
   }
   ```

4. IOC解耦过程

   > 第一步：xml配置文件，配置创建的对象
   >
   > <bean id="dao" class="com.test.xxx.UserDao"></bean>
   >
   > 第二步：有service类和到dao类，创建工厂类
   >
   > ```java
   > class UserFactory{
   >     public static UserDao getDao(){
   >         String classValue = class属性值；//1. xml解析
   >         Class clazz = Class.forName(classValue);//2.通过反射创建对象
   >         return （UserDao)clazz.newInstance() 
   >     }
   > }
   > ```

##### 三、IOC接口(BeanFactory)

1. IOC思想基于IOC容器完成，IOC容器底层就是对象工厂

2. Spring提供IOC容器实现两种方式:

   1. BeanFactory:IOC容器基本实现，是Spring内部的使用接口，不提供开发人员使用

      > 加载配置获取文件的时候不会创建对象，在获取/使用对象的时候才去创建对象
      >
      > 什么时候用什么时候创建对象

   2. ApplicationContext：BeanFactory接口的子接口，提供更强大的功能，一般由开发人员进行使用

      > 加载配置文件的时候就会把配置文件中的对象进行创建

   3. ApplicationContext的实现类

      FileSystemXmlApplicationContext:磁盘下配置文件路径

      ClassPathXmlApplicationContext：类路径下的配置文件

##### 四、IOC操作Bean管理

1. **什么是Bean管理**
   1. Bean管理
   2. Spring创建对象
   3. Spring注入属性
2. **Bean管理操作有两种方式**
   1. IOC操作Bean管理(基于xml)
   2. IOC操作Bean管理(基于注解)

##### 五、IOC操作Bean管理(基于xml)

1. **基于xml方式创建对象**

   > 1. 在spring配置文件中使用bean标签，标签里面添加对应的属性，就可以实现对象创建
   >
   >    <bean id="user" class="com.xxx.xxx.User"></bean>
   >
   > 2. 在bean标签有很多属性，介绍常用的属性：
   >
   >    1. id属性：创建对象的唯一标识
   >    2. class属性：类全路径(包类路径) 
   >
   > 3. 创建对象时候，默认也是执行无参构造方法完成对象创建

2. **基于xml方式注入属性**

   1. DI：依赖注入，就是注入属性

3. **第一种注入方式：使用set方法进行注入**

   1. 创建类，定义属性和对应的set方法

      ```java
      class Book{
          private String bName;
          public void setBName(String bName){
              this.bName = bName;
          }
      }
      ```

   2. 在spring配置文件配置对象创建，配置属性注入

      > set方法注入属性
      >
      > <bean id="book" class="com.xxx.xxx.Book">
      >
      > ​	name: 类里面的属性名称
      >
      > ​	value：向属性注入的值
      >
      > ​	<property name="bName" value="红楼梦"></property>
      >
      > </bean>

4. **第二种注入方式：使用有参数构造进行注入**

   1. 创建类，定义属性，创建属性对应有参数的构造方法

      ```java
      public class Orders {
          //属性
          private String oName;
          private String address;
          //有参数构造
          public Orders(String oName, String address) {
              this.oName = oName;
              this.address = address;
          }
      }
      ```

   2. 在Spring配置文件中进行配置

      > 有参数构造注入属性
      >
      > <bean id="orders" class="com.xxx.xxx.Orders">
      >
      >  	<constructor-arg name="oname" value="电脑"></constructor-arg>
      >
      > ​	 <constructor-arg name="address" value="China"></constructor-arg>
      >
      > </bean>

5. **p名称空间注入(简化xml配置)**

   底层还是采用set注入，简化xml配置文件多属性注入

   1. **使用p名称空间注入，可以简化基于xml配置方式**

      > 第一步添加p名称空间在配置文件中
      >
      > <beans  xmls:p="https://www.springframework.org/schema/p">
      >
      > 第二步 进行属性注入，在bean标签里面进行操作	
      >
      > ​	<bean id="book" class="com.xxx.xxx.Book" p:bname="三国演义" p:bauthor="罗贯中" >
      >
      > ​	</bean>
      >
      > </beans>

##### 六、IOC操作Bean管理(xml注入其他类型属性)

1. **字面值**

   1. null值

      <property name="address">

      ​	<null/>

      </property>

   2. 属性值包含特殊符号

      <property name="address">

      ​	<value><![CDATA[<<南京>>]]></value>

      </property>
   
2. 外部Bean注入

   > 第一步：创建两个类service类和dao类
   >
   > ```java
   > 
   > public interface UserDao {
   >     public void update();
   > }
   > 
   > public class UserDaoImpl implements UserDao{
   >     @Override
   >     public void update() {
   >         System.out.println("Dao update.....");
   >     }
   > }
   > ```
   >
   > 第二步：在service调用dao类里面的方法
   >
   > ```java
   > public class UserService {
   >     //创建UserDao类型属性，生成set方法
   >     private UserDao userDao;
   >     public void setUserDao(UserDao userDao) {
   >         this.userDao = userDao;
   >     }
   >     public void add(){
   >         System.out.println("service add......");
   > //        原始方式：
   > //        创建UserDao对象
   > //        UserDaoImpl userDao = new UserDaoImpl();
   >         userDao.update();
   >     }
   > }
   > ```
   >
   > 第三步：在spting配置文件中进行配置
   >
   > ```xml
   > <!--    service和dao对象创建-->
   >     <bean id="userService" class="com.test.spring5.service.UserService">
   > <!--        注入UserDao对象-->
   > <!--        ref:创建的userDao对象bean标签-->
   >         <property name="userDao" ref="userDao"></property>
   >     </bean>
   >     <bean id="userDao" class="com.test.spring5.dao.UserDaoImpl"></bean>
   > ```

3. 内部Bean注入和级联赋值

   一对多关系：部门和员工，一个部门有多个员工，一个员工属于某一个部门，部门是一，员工是多

   ```java
   // 部门类
   public class Dept {
       private String dName;
       public void setdName(String dName) {
           this.dName = dName;
       }
   }
   ```

   ```java
   // 员工类
   public class Emp {
       private String eName;
       private String gender;
       private Dept dept;
       public void setDept(Dept dept) {
           this.dept = dept;
       }
       public void seteName(String eName) {
           this.eName = eName;
       }
       public void setGender(String gender) {
           this.gender = gender;
       }
   }
   ```

   对spring配置文件中进行配置

   ```xml
   <!--    内部bean-->
       <bean id="emp" class="com.test.spring5.bean.Emp">
   <!--        设置两个普通属性-->
           <property name="eName" value="lucy"></property>
           <property name="gender" value="女"></property>
   <!--        设置对象类型属性-->
           <property name="dept">
               <bean id="dept" class="com.test.spring5.bean.Dept">
                   <property name="dName" value="保安部"></property>
               </bean>
           </property>
       </bean>
   ```

4. 级联赋值

   第一种写法：xml配置文件

   ```xml
      <!--    级联赋值-->
       <bean id="emp" class="com.test.spring5.bean.Emp">
           <!--        设置两个普通属性-->
           <property name="eName" value="lucy"></property>
           <property name="gender" value="女"></property>
           <!--        设置对象类型属性-->
   <!--        级联赋值-->
           <property name="dept" ref="dept"></property>
       </bean>
       <bean id="dept" class="com.test.spring5.bean.Dept">
           <property name="dName" value="财务部"></property>
       </bean>
   ```

   第二种写法：生成get方法并修改配置文件

   ```xml
   <!--    级联赋值-->
       <bean id="emp" class="com.test.spring5.bean.Emp">
           <!--        设置两个普通属性-->
           <property name="eName" value="lucy"></property>
           <property name="gender" value="女"></property>
           <!--        设置对象类型属性-->
   <!--        级联赋值-->
           <property name="dept" ref="dept"></property>
           <property name="dept.dName" value="技术部"></property>
       </bean>
       <bean id="dept" class="com.test.spring5.bean.Dept">
           <property name="dName" value="财务部"></property>
       </bean>
   ```

##### 七、IOC操作Bean管理(xml注入集合属性)

1. 注入数组类型属性

2. 注入list集合类型属性

3. 注入Map集合类型属性

   第一步：定义一个类，定义数组、list集合、map集合、set集合属性，生成对应的set方法

   ```java
   public class Stu {
       //1.数组类型属性
       private String[] courses;
       //2.list集合类型属性
       private List<String> list;
       //3.map集合类型属性
       private Map<String,String> maps;
       //4.set集合类型属性
       private Set<String> sets;
       public void setList(List<String> list) {
           this.list = list;
       }
       public void setMaps(Map<String, String> maps) {
           this.maps = maps;
       }
       public void setCourses(String[] courses) {
           this.courses = courses;
       }
   }
   ```

   第二步：配置spring配置文件进行配置
   
   ```xml
   <!--    集合类型属性注入-->
       <bean id="stu" class="com.test.spring5.collectiontype.Stu">
   <!--      数组属性注入-->
           <property name="courses">
               <array>
                   <value>语文</value>
                   <value>数学</value>
                   <value>英语</value>
               </array>
           </property>
   <!--        list集合属性注入-->
           <property name="list">
               <list>
                   <value>张三</value>
                   <value>李四</value>
                   <value>王五</value>
               </list>
           </property>
   <!--        map集合属性注入-->
           <property name="maps">
                <map>
                    <entry key="Java" value="java基础"></entry>
                    <entry key="PHP" value="php"></entry>
                </map>
           </property>
   <!--        set集合属性注入-->
           <property name="sets">
               <set>
                   <value>MySQL</value>
                   <value>Redis</value>
               </set>
           </property>
       </bean>
   ```
   
4. 在集合里面设置对象类型的值

   ```xml
   <!--    创建多个course对象-->
       <bean id="course1" class="com.test.spring5.collectiontype.Course">
           <property name="cname" value="Spring5"></property>
       </bean>
       <bean id="course2" class="com.test.spring5.collectiontype.Course">
           <property name="cname" value="MyBatis"></property>
       </bean>
   ```

   ```xml
   <!--        注入list集合类型，值是对象-->
           <property name="courseList">
               <list>
                   <ref bean="course1"></ref>
                   <ref bean="course2"></ref>
               </list>
           </property>
   ```

5. 把集合注入部分提取出来

   第一步：先在Spring配置文件中引入名称空间(util)

   ```xml
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xmlns:util="http://www.springframework.org/schema/util"
          xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                              http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util.xsd
   ">
   ```

   第二步：使用util标签完成list集合注入提取

   ```xml
   <!--    1.提取list集合类型属性注入-->
       <util:list id="bookList">
           <value>红楼梦</value>
           <value>西游记</value>
           <value>易筋经</value>
       </util:list>
   <!--    2.提取list注入使用-->
       <bean id="book" class="com.test.spring5.collectiontype.Book">
           <property name="list" ref="bookList"></property>
       </bean>
   ```

##### 八、IOC操作Bean管理(FactoryBean)

1. Spring有两种类型的Bean，一种普通的bean，另外一种是工厂bean(FactoryBean)

   > 普通Bean：在配置文件中定义Bean类型就是返回类型
   >
   > 工厂Bean：在配置文件定义Bean类型可以和返回类型不一样

   第一步：创建类，让这个类作为工厂Bean，实现接口FactoryBean

   第二步：实现接口里面的方法，在实现的方法中定义返回的Bean类型

   ```java
   public class MyBean implements FactoryBean<Course> {
       //返回对象实例
       //定义返回Bean
       @Override
       public Course getObject() throws Exception {
           Course course = new Course();
           course.setCname("abc");
           return course;
       }
       //返回类型
       @Override
       public Class<?> getObjectType() {
           return null;
       }
       //它是否是个单例
       @Override
       public boolean isSingleton() {
           return FactoryBean.super.isSingleton();
       }
   }
   ```

   ```java
    @Test
   public void testCollection3(){
   ApplicationContext context = new ClassPathXmlApplicationContext("bean3.xml");
           Course course = context.getBean("myBean", Course.class);
           System.out.println(course);
       }
   ```

   ```xml
   <bean id="myBean" class="com.test.spring5.factorybean.MyBean"></bean>
   ```

九、IOC操作Bean管理(Bean作用域)

1. 在Spring里面，设置创建Bean实例是单实例还是多实例


2. 在Spring里面，默认情况下，bean是单实例对象


   ```java
   @Test
       public void testCollection4(){
           ApplicationContext context = new ClassPathXmlApplicationContext("bean4.xml");
           Book book1 = context.getBean("book", Book.class);
           Book book2 = context.getBean("book", Book.class);
        //单实例输出对象相同
           System.out.println(book1);
           System.out.println(book2);
       }
   ```

3. 如何设置单实例还是多实例


   1. 在Spring配置文件bean标签里面有属性(scope)用于设置单实例还是多实例

   2. scope属性值

      第一个值：默认值，singleton，表示是单实例对象

      第二个值：prototype，表示是多实例对象

      ```xml
      <bean id="book" class="com.test.spring5.collectiontype.Book" scope="prototype">
           <property name="list" ref="bookList"></property>
      </bean>
      ```

   3. singleton和prototype区别

      > 1. singleton  单实例，prototype  多实例
      > 2. 设置scope值是singleton时候，加载spring配置文件时候就会创建单实例对象。设置scope值是prototype时候，不是在加载spring配置文件时候创建对象，在调用getBean方法时候创建多实例对象。


##### 十、IOC操作Bean管理(bean生命周期)

1. 生命周期

   从对象的创建到对象销毁的过程

2. bean的生命周期

   1. 通过构造器创建bean实例(无参构造)
   2. 为bean里面的属性设置值和对其他bean的引用(调用set方法)
   3. 调用bean的初始化的方法(需要进行配置)
   4. bean可以使用了(对象获取到了)
   5. 当容器关闭的时候，调用bean的销毁的方法(需要进行配置销毁的方法)

   ```java
   public class Orders {
       //无参构造
       public Orders() {
           System.out.println("第一步：执行无参数构造创建Bean实例");
       }
       private String oname;
       public void setOname(String oname) {
           this.oname = oname;
           System.out.println("第二步：调用set方法设置属性值");
       }
       //创建执行的初始化的方法
       public void initMethod(){
           System.out.println("第三步：执行初始化的方法");
       }
       //创建执行的销毁的方法
       public void destroyMethod(){
           System.out.println("第五步：执行销毁的方法");
       }
   ```

   ```xml
   <bean id="orders" class="com.test.spring5.bean.Orders" init-method="initMethod" destroy-method="destroyMethod">
           <property name="oname" value="手机"></property>
       </bean>
   ```

   ```java
    @Test
       public void testBean(){
           ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("bean5.xml");
           Orders orders = context.getBean("orders", Orders.class);
           System.out.println("第四步：获取到创建bean实例对象");
           System.out.println(orders);
           //手动让bean实例销毁
           context.close();
       }
   ```

3. bean的后置处理器

   1. 通过构造器创建bean实例(无参构造)

   2. 为bean里面的属性设置值和对其他bean的引用(调用set方法)

   3. 把bean实例传递bean后置处理器的方法      -----postProcessBeforeInitialization

   4. 调用bean的初始化的方法(需要进行配置)

   5. 把bean实例传递bean后置处理器的方法      -----postProcessAfterInitialization

   6. bean可以使用了(对象获取到了)

   7. 当容器关闭的时候，调用bean的销毁的方法(需要进行配置销毁的方法)

      后置处理器  例：

      1. 创建类，实现接口BeanPostProcessor，创建后置处理器

      ```java
      public class MyBeanPost implements BeanPostProcessor {
          @Override
          public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
              System.out.println("在初始化之前执行的方法-------后置处理器");
              return bean;
          }
          @Override
          public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
              System.out.println("在初始化之后执行的方法------后置处理器");
              return bean;
          }
      }
      ```

      ```xml
      <!--    配置后置处理器-->
          <bean id="myBeanPost" class="com.test.spring5.bean.MyBeanPost"></bean>
      ```


##### 十一、IOC操作Bean管理(xml自动装配)

​	根据指定装配规则(属性名称或者属性类型)，spring自动将匹配的属性值进行注入

 1. 根据属性的名称自动注入

    ```xml
    <!--    实现自动装配-->
    <!--    bean标签中有个属性autowire，配置自动装配-->
    <!--    autowire两个值：byName根据属性名称注入    byType根据属性类型注入-->
        <bean id="emp" class="com.test.spring5.autowire.Emp" autowire="byName">
        </bean>
        <bean id="dept" class="com.test.spring5.autowire.Dept"></bean>
    ```

 2. 根据属性的类型自动注入(相同类型的属性不能定义多个)

    ```xml
    <!--    实现自动装配-->
    <!--    bean标签中有个属性autowire，配置自动装配-->
    <!--    autowire两个值：byName根据属性名称注入    byType根据属性类型注入-->
        <bean id="emp" class="com.test.spring5.autowire.Emp" autowire="byType">
        </bean>
        <bean id="dept" class="com.test.spring5.autowire.Dept"></bean>
    ```

##### 十二、IOC操作Bean管理(引入外部属性文件)

1. 直接配置数据库信息

   1. 配置德鲁伊连接池
   2. 引入德鲁伊连接池依赖jar包

   ```xml
   <!--    配置连接池-->
       <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
           <property name="driverClassName" value="com.mysql.jdbc.Driver"></property>
           <property name="url" value="jdbc:mysql://localhost:3306/userDb"></property>
           <property name="username" value="root"></property>
           <property name="password" value="root"></property>
       </bean>
   ```

2. 引入外部属性文件配置数据库连接池

   1. 创建外部属性文件，properties格式文件，写数据库信息

      ```properties
      prop.driverClassName=com.mysql.jdbc.Driver
      prop.url=jdbc:mysql://localhost:3306/userDb
      prop.username=root
      prop.password=root
      ```

   2. 把外部properties属性文件引入spring配置文件中

      1. 引入context名称空间

         ```xml
         <beans xmlns="http://www.springframework.org/schema/beans"
                xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
                xmlns:context="http://www.springframework.org/schema/context"
                xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                                    http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd
         ">
         ```

      2. 在spring配置引入外部文件

         ```xml
         <!--    引入外部的属性文件-->
             <context:property-placeholder location="classpath:jdbc.properties"/>
             <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
                 <property name="driverClassName" value="${prop.driverClassName}"></property>
                 <property name="url" value="${prop.url}"></property>
                 <property name="username" value="${prop.username}"></property>
                 <property name="password" value="${prop.password}"></property>
             </bean>
         ```

##### 十三、IOC操作Bean管理(基于注解方式)

1. 什么是注解

   注解是代码特殊标记，格式：@注解名称(属性名称=属性值，属性名称=属性值...)

   使用注解，注解可以作用在类上面，方法上面，属性上面

   使用注解的目的：简化xml配置

2. Spring针对Bean管理(创建对象)中创建对象提供注解

   1. @Component
   2. @Service
   3. @Controller
   4. @Repository

   上面的四个注解功能是一样的，都是用来创建Bean的实例

3. 基于注解方式实现对象创建

   1. 第一步引入依赖
   2. 开启组件扫描

   ```xml
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xmlns:context="http://www.springframework.org/schema/context"
          xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
   http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd
   ">
   <!--    开启组件扫描
       1.扫描多个包，多个包之间使用逗号隔开
       2.扫描包上层目录
   -->
       <context:component-scan base-package="com.test.spring5"></context:component-scan>
   ```
   
   3. 创建类，在类上面添加创建对象得注解
   
   ```java
      //注解里面value属性值可以省略
      //默认值是类名称，首字母小写
      @Service(value = "userService")//类似 <bean id="userService" class="..."></bean>
      public class UserService {
          public void add(){
              System.out.println("service add.....");
          }
      }
   ```
   
   4. 开启组件扫描细节配置
   
   ```xml
      <!--    示例1
          use-default-filters="false"   表示现在不使用默认的filters，自己配置filter
          context:include-filter        扫描那些内容
      -->
          <context:component-scan base-package="com.test.spring5" 
                                  use-default-filters="false">
              <context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
          </context:component-scan>
   ```
   
   ```xml
   <!--    示例2
       context:exclude-filter     设置那些内容不进行扫描
   -->
       <context:component-scan base-package="com.test.spring5" >
           <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
       </context:component-scan>
   ```

##### 十四、IOC操作Bean管理(基于注解属性注入)

1. **@Autowired**：根据属性类型进行自动装配

   例：

   第一步 吧service和dao对象创建，在service和dao类添加创建对象注解

   第二步 在service注入dao对象，在service类添加dao类属性，在属性上面使用注解

   ```java
   @Service
   public class UserService {
       //定义dao类型属性
       //不需要添加set方法
       //添加注入属性的注解
       @Autowired
       private UserDao userDao;
       public void add(){
           System.out.println("service add.....");
       }
   }
   ```

   

2. **@Qualifier**：根据属性名称进行注入

   @Qualifier需要配合@Autowired注解进行使用

   ```java
   @Repository(value = "userDapImpl1")
   public class UserDaoImpl implements UserDao{
       @Override
       public void add() {
           System.out.println("dao  add......");
       }
   }
   ```

   ```java
    @Autowired  //根据类型进行注入
       @Qualifier(value = "userDapImpl1")
       private UserDao userDao;
       public void add(){
           System.out.println("service add.....");
           userDao.add();
       }
   ```

3. **@Resource**：可以根据类型注入，也可以根据名称注入

   ```java
    import javax.annotation.Resource;
   
    @Resource( name = "userDapImpl1")  //根据名称进行注入
    @Resource   //根据类型注入 
   ```

4. **@Value**：注入普通类型属性

   ```java
   @Value(value = "Tom")  //普通类型属性注入
       private String name;
       public void add(){
           System.out.println("service add.....");
           System.out.println("service-name-"+name);
           userDao.add();
       }
   ```

5. 纯注解开发

   1. 创建配置类，替代xml配置

      ```java
      @Configuration   //作为配置类，替代xml配置文件
      @ComponentScan(basePackages = {"com.test.spring5"}) //开启
      public class SpringConfig {
      }
      ```

   2. 编写测试类

      ```java
      @Test
          public void testService2(){
              //加载配置类
              ApplicationContext context = new AnnotationConfigApplicationContext(SpringConfig.class);
              UserService userService = context.getBean("userService", UserService.class);
              System.out.println(userService);
              userService.add();
          }
      ```

## 三、AOP

#### 一、什么是AOP(概念)

​	AOP为Aspect Oriented Programming 面向切面编程(方面)，利用AOP可以对业务逻辑的各个部分进行隔离，从而使得业务逻辑各个部分之间的耦合度降低，提高程序的可重用性，同时提高开发效率

​	`不通过修改源代码的方式添加新功能`

​	通俗描述：不通过修改源代码方式，在主干功能里面添加新功能

#### 二、AOP底层原理

1. AOP底层使用动态代理
   1. 第一种有接口的情况(JDK动态代理)
   2. 第二种没有接口的情况(CGLib动态代理)

##### 2.1、AOP底层原理(JDK动态代理)

1. 使用JDK动态代理，使用Proxy类里面的方法创建代理对象

   java.lang.reflect.Proxy

   主要方法：

   | 修饰符和类型  | 方法与描述                                                   |
   | ------------- | ------------------------------------------------------------ |
   | static Object | newProxyInstance（）返回指定接口的代理类的实例，该接口将方法调用分派给指定的调用处理程序。 |

   里面有三个参数
   
   | 参数名                | 说明                                       |
   | --------------------- | ------------------------------------------ |
   | ClassLoader loader    | 类加载器                                   |
   | Class<?>[] interfaces | 增强方法所在类，这个类的接口，支持多个接口 |
   | InvocationHandler h   | 实现这个接口，创建代理对象，写增强的方法   |
   
2. 编写JDK动态代理代码

   1. 创建接口，定义方法

      ```java
      public interface UserDao {
          public int add(int a, int b);
          public String update(String id);
      }
      ```

   2. 创建接口实现类，实现方法

      ```java
      public class UserDaoImpl implements UserDao{
          @Override
          public int add(int a, int b) {
              return a+b;
          }
          @Override
          public String update(String id) {
              return id;
          }
      }
      ```

   3. 使用Proxy类创建接口代理对象(增强)

      ```java
      public class JDKProxy {
          public static void main(String[] args) {
              //创建接口实现类代理对象
              Class[] interfaces = {UserDao.class};
      //        Proxy.newProxyInstance(JDKProxy.class.getClassLoader(), interfaces, new InvocationHandler() {
      //            @Override
      //            public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
      //                return null;
      //            }
      //        });
              UserDaoImpl userDao = new UserDaoImpl();
              UserDao dao = (UserDao) Proxy.newProxyInstance(JDKProxy.class.getClassLoader(), interfaces, new UserDaoProxy(userDao));
              int result = dao.add(1, 2);
              System.out.println("result:"+result);
          }
      }
      //创建代理对象代码
      class UserDaoProxy implements InvocationHandler{
          //1 把创建的是谁的代理对象，把谁传递过来
          //有参数的构造传递
          private Object obj;
          public UserDaoProxy(Object obj) {
              this.obj = obj;
          }
          //对象被创建就会调用invoke方法
          @Override
          public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
              //增强的逻辑
              //方法之前
              System.out.println("方法执行之前......"+method.getName()+"  :传递的参数..."+ Arrays.toString(args));
              //被增强的方法执行
              Object res = method.invoke(obj, args);
              //方法之后
              System.out.println("方法执行之后......"+ obj);
              return res;
          }
      }
      ```

#### 三、AOP(术语)

​	术语就是例如，汽车发动机又叫引擎

1. 连接点

   > 类里面那些方法可以被增强，这些方法称为连接点

2. 切入点

   > 实际被真正增强的方法，称为切入点

3. 通知(增强)

   > 1. 实际增强的逻辑部分称为通知(增强)
   > 2. 通知有多种类型
   >    1. 前置通知：被增强的原始方法之前执行的逻辑
   >    2. 后置通知：被增强的原始方法之后执行的逻辑
   >    3. 环绕通知：被增强的原始方法之前后执行的逻辑
   >    4. 异常通知：被增强的原始方法出现异常后执行的逻辑
   >    5. 最终通知：如同try~catch后面的finally类似

   ```java
     //前置通知
       //Before 注解表示作为前置通知
       @Before(value = "execution()")
       //后置通知(返回值通知)
       @AfterReturning(value = "execution()")
       //环绕通知
       @Around(value = "execution()")
       //异常通知
       @AfterThrowing(value = "execution()")
       //最终通知
       @After(value = "execution()")  
   ```

4. 切面

   > 把通知应用到切入点过程，是一个动作

#### 四、AOP操作

##### 4.1 准备工作

1. Spring框架中一般都是基于AspectJ实现AOP操作

   1. 什么是AspectJ

      > AspectJ不是Spring组成部分，独立AOP框架，一般把AspectJ和Spring框架一起使用，进行AOP操作

2. 基于`AspectJ`实现AOP操作

   1. 基于xml配置文件
   2. 基于注解方式使用(常用)

3. 在项目工程里面引入AOP相关依赖

   > spring-aspects-5.2.6.RELEASE.jar
   > spring-aop-5.2.6.RELEASE.jar
   > com.springsource.org.aspectj.weaver-1.6.8.RELEASE.jar
   > com.springsource.org.aopalliance-1.0.0.jar
   > com.springsource.net.sf.cglib-2.2.0.jar

4. 切入点表达式

   > 1. 切入点表达式作用：知道对那个类里面的那个方法进行增强
   >
   > 2. 语法结构：
   >
   >    excution([权限修饰符]\[返回类型]\[类的全路径]\[方法名称]([参数列表]))
   >    
   >    举例1：对com.test.dao.BoolDao类里面的add进行增强
   >    
   >    `xcution(* com.test.dao.BoolDao.add(..));`
   >    
   >    举例2：对com.test.dao.BoolDao类里面的所有方法增强
   >    
   >    `excution(* com.test.dao.BoolDao.*(..));`
   >    
   >    举例3：对com.test.dao.BoolDao类里面的所有类和所有类中的所有方法增强
   >    
   >    `excution(* com.test.dao.\*.*(..));`

##### 4.2 AOP操作(AspectJ注解)

1. 创建类，在类里面定义方法，被增强类

   ```java
   //被增强的类
   public class User {
       public void add(){
           System.out.println("User add......");
       }
   }
   ```

2. 创建增强类(编写增加的逻辑)

   在增强的类里面，创建不同的方法，让不同的方法代表不同的通知类型

   ```java
   //增强的类
   public class UserProxy {
       //前置通知
       public void before(){
           System.out.println("before......   前置通知");
       }
   }
   ```

3. 进行通知的配置

   1. 在spring配置文件中，开启注解扫描

      ```xml
      <?xml version="1.0" encoding="UTF-8"?>
      <beans xmlns="http://www.springframework.org/schema/beans"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xmlns:context="http://www.springframework.org/schema/context"
             xmlns:aop="http://www.springframework.org/schema/aop"
             xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                                 http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd
                                 http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd
      ">
      <!--    开启注解扫描-->
          <context:component-scan base-package="com.test.spring5.aopanno"></context:component-scan>
      </beans>
      ```

   2. 使用注解创建User和UserProxy对象

      ```java
      @Component
      public class User {
          public void add(){
              System.out.println("User add......");
          }
      }
      ```

      ```java
      @Component
      public class UserProxy {
          //前置通知
          public void before(){
              System.out.println("before......   前置通知");
          }
      }
      ```

      

   3. 在增强类上面添加注解@Aspect

      ```java
      @Component
      @Aspect  //生成代理对象
      public class UserProxy {
          //前置通知
          public void before(){
              System.out.println("before......   前置通知");
          }
      }
      ```

   4. 在Spring配置文件中开启生成代理对象

      ```xml
      <!--    开启Aspect生成代理对象-->
          <aop:aspectj-autoproxy></aop:aspectj-autoproxy>
      ```

   5. 配置不同类型的通知

      在我增强类的里面，作为通知方法上面添加通知类型注解，使用切入点表达式配置

      ```java
      //增强的类
      @Component
      @Aspect  //生成代理对象
      public class UserProxy {
          //前置通知
          //Before 注解表示作为前置通知
          @Before(value = "execution(* com.test.spring5.aopanno.User.add(..))")
          public void before(){
              System.out.println("before......   前置通知");
          }
      
          //后置通知(返回值通知)
          @AfterReturning(value = "execution(* com.test.spring5.aopanno.User.add(..))")
          public void afterReturning(){
              System.out.println("AfterReturning......   后置通知");
          }
      
          //环绕通知
          @Around(value = "execution(* com.test.spring5.aopanno.User.add(..))")
          public void around(ProceedingJoinPoint proceedingJoinPoint) throws Throwable {
              System.out.println("环绕之前......");
              //被增强的方法执行
              proceedingJoinPoint.proceed();
              System.out.println("环绕之后......");
          }
      
          //异常通知
          @AfterThrowing(value = "execution(* com.test.spring5.aopanno.User.add(..))")
          public void afterThrowing(){
              System.out.println("AfterThrowing......   前置通知");
          }
      
          //最终通知
          @After(value = "execution(* com.test.spring5.aopanno.User.add(..))")
          public void after(){
              System.out.println("After......  最终通知");
          }
      }
      ```

   6. 相同切入点进行抽取

      ```java
       //相同切入点抽取
          @Pointcut(value = "execution(* com.test.spring5.aopanno.User.add(..))")
          public void pointDemo(){
          }
          //前置通知
          //Before 注解表示作为前置通知
          @Before(value = "pointDemo()")
          public void before(){
              System.out.println("before......   前置通知");
          }
      ```

   7. 有多个增强类对同一个方法增强，设置增强类优先级

      在增强类上面添加注解@Order(值)，数字类型的值，数值越小优先级越高

      ```java
      @Component
      @Aspect
      @Order(1)  //优先级
      public class PersonProxy {
      ```
      
   8. 完全使用注解开发
   
      ```java
      //配置类不需要创建xml配置文件
      @Configuration
      @ComponentScan(basePackages = {"com.test.spring5"})
      @EnableAspectJAutoProxy(proxyTargetClass = true)
      public class ConfigAop {
      }
      ```
   

##### 4.3 AOP操作(AspectJ配置文件)

1. 创建两个类，增强类和被增强类，创建方法

   ```java
   public class Book {
       public void buy(){
           System.out.println("buy......");
       }
   }
   ```

   ```java
   public class BookProxy {
       public void before(){
           System.out.println("before......");
       }
   }
   ```

2. 在spring配置文件中创建两个类对象

   ```xml
   <!--    创建两个类的对象-->
       <bean id="book" class="com.test.spring5.aopxml.Book"></bean>
       <bean id="bookProxy" class="com.test.spring5.aopxml.BookProxy"></bean>
   ```

3. 在spring配置文件中配置切入点

   ```java
   <!--    创建两个类的对象-->
       <bean id="book" class="com.test.spring5.aopxml.Book"></bean>
       <bean id="bookProxy" class="com.test.spring5.aopxml.BookProxy"></bean>
   <!--    配置AOP增强-->
       <aop:config>
   <!--        切入点-->
           <aop:pointcut id="p" expression="execution(* com.test.spring5.aopxml.Book.buy(..))"/>
   <!--        配置切面-->
           <aop:aspect ref="bookProxy">
   <!--            增强作用在具体的方法上-->
               <aop:before method="before" pointcut-ref="p"/>
           </aop:aspect>
       </aop:config>
   ```


## 四、JDBC Templatek

#### 一、概念和准备

1. 什么是JDBCTemplatek

   Spring框架对JDBC进行封装，使用JDBCTemplatek方便实现对数据库操作

2. 准备工作

   1. 引入相关的依赖

      > spring-tx-5.2.6.RELEASE.jar
      > spring-orm-5.2.6.RELEASE.jar
      > spring-jdbc-5.2.6.RELEASE.jar
      > druid-1.1.9.jar
      > mysql-connector-java-8.0.28.jar

   2. 在spring配置文件配置数据库连接池

      ```xml
      <!--    数据库连接池-->
          <bean id="database" class="com.alibaba.druid.pool.DruidDataSource" destroy-method="close">
              <property name="url" value="jdbc:mysql:///user_db"/>
              <property name="username" value="root"/>
              <property name="password" value="root"/>
              <property name="driverClassName" value="com.mysql.cj.jdbc.Driver"/>
          </bean>
      ```

   3. 配置JDBCTemplate对象,注入DataSource

      ```xml
      <!--    创建JDBCTemplate对象-->
          <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
      <!--        注入dataSource-->
              <property name="dataSource" ref="database"/>
          </bean>
      ```

   4. 创建Service类，创建dao类，在dao注入jdbcTemlate对象
   
      ```xml
      <!--    开启组件扫描-->
          <context:component-scan base-package="com.test.spring5"></context:component-scan>
      ```
   
      ```java
      @Service
      public class BookService {
          //注入dao
          @Autowired
          private BookDao bookDao;
      }
      ```
   
      ```java
      @Repository
      public class BookDaoImpl implements BookDao{
          //注入jdbcTemplate
          @Autowired
          private JdbcTemplate jdbcTemplate;
      }
      ```

#### 二、JDBCTemplate操作数据库

##### 一、添加

1. 对应数据库创建实体类

   ```java
   public class User {
       private String userId;
       private String username;
       private String ustatus;
       public String getUserId() {
           return userId;
       }
       public void setUserId(String userId) {
           this.userId = userId;
       }
   ```

2. 编写service和dao

   1. 在dao层进行数据库添加操作

   2. 调用jdbctemplate对象里面的update方法实现添加操作

      | 参数            | 说明                    |
      | --------------- | ----------------------- |
      | String  SQL     | sql语句                 |
      | Object...  args | 可变参数，设置sql语句值 |

      ```java
      @Repository
      public class BookDaoImpl implements BookDao{
          //注入jdbcTemplate
          @Autowired
          private JdbcTemplate jdbcTemplate;
          //添加的方法
          public void add(Book book){
              //创建sql语句
              String sql = "insert into t_book values(?,?,?)";
              //调用方法实现
      //        int rows = jdbcTemplate.update(sql, book.getUserId(), book.getUsername(), book.getUstatus());
              Object[] args = { book.getUserId(), book.getUsername(), book.getUstatus()};
              int rows = jdbcTemplate.update(sql,args);
              System.out.println(rows);
          }
      }
      ```

3. 测试类

   ```java
   public class TestBook {
       @Test
       public void testJDBCTemplate(){
           ApplicationContext context = new ClassPathXmlApplicationContext("bean1.xml");
           BookService bookService = context.getBean("bookService", BookService.class);
           Book book = new Book();
           book.setUserId("1");
           book.setUsername("红露面");
           book.setUstatus("a");
           bookService.addBook(book);
       }
   }
   ```

##### 二、修改

1. BookService添加修改方法

   ```java
   //修改的方法
       @Override
       public void updateBook(Book book) {
           String sql = "update t_book set username=?,ustatus=? where user_id=?";
           Object[] args = { book.getUsername(), book.getUstatus(),book.getUserId()};
           int update = jdbcTemplate.update(sql, args);
           System.out.println("修改了"+update+"记录");
       }
   ```

2. 实现修改方法

   ```java
   //修改的方法
       @Override
       public void updateBook(Book book) {
           String sql = "update t_book set username=?,ustatus=? where user_id=?";
           Object[] args = { book.getUsername(), book.getUstatus(),book.getUserId()};
           int update = jdbcTemplate.update(sql, args);
           System.out.println("修改了"+update+"记录");
       }
   ```

3. 测试

   ```java
   Book book = new Book();
           book.setUserId("1");
           book.setUsername("许仙");
           book.setUstatus("AAAA");
           bookService.updateBook(book);
   ```

##### 三、删除

```java
//修改的方法
    @Override
    public void updateBook(Book book) {
        String sql = "update t_book set username=?,ustatus=? where user_id=?";
        Object[] args = { book.getUsername(), book.getUstatus(),book.getUserId()};
        int update = jdbcTemplate.update(sql, args);
        System.out.println("修改了"+update+"记录");
    }
```

测试

```java
Book book = new Book();
        bookService.deleteBook("1");
```

##### 四、查询

1. **查询返回某个值**

   1. 查询表里面有多少条记录，返回某个值

   2. 使用jdbcTemplate实现返回某个值代码

      queryForObject

      | 参数                  | 说明          |
      | --------------------- | ------------- |
      | String  SQL           | sql语句       |
      | Class<T> requiredType | 返回类型Class |

      ```java
      @Override
          public int selectCount() {
              String sql = "select count(*) from t_book";
              Integer count = jdbcTemplate.queryForObject(sql, Integer.class);
              return count;
          }
      ```

   3. 测试

      ```java
       //查询
              int count = bookService.findCount();
              System.out.println(count);
      ```

2. **查询返回对象**

   1. 场景：查询图书的详情页面

   2. JDBCTemplate实现查询返回对象

      | 参数                   | 说明                                                         |
      | ---------------------- | ------------------------------------------------------------ |
      | String SQL             | sql语句                                                      |
      | RowMapper<T> rowMapper | rowmapper,是接口，返回不同类型数据，用接口里面实现类完成数据封装 |
      | object... args         | sql语句值                                                    |

      ```java
      @Override
          public Book findBookInfo(String id) {
              String sql = "select * from t_book where user_id=?";
              //调用方法
              Book book = jdbcTemplate.queryForObject(sql, new BeanPropertyRowMapper<Book>(Book.class), id);
              return book;
          }
      ```

   3. 测试

      ```java
      //查询返回对象
              Book one = bookService.findOne("1");
              System.out.println(one);
      ```

3. **查询返回集合**

   1. 场景：查询图书列表分页...

   2. 调用jdbcTemplate方法实现查询返回集合

      query()

      | 参数                   | 说明                                                         |
      | ---------------------- | ------------------------------------------------------------ |
      | String SQL             | sql语句                                                      |
      | RowMapper<T> rowMapper | rowmapper,是接口，返回不同类型数据，用接口里面实现类完成数据封装 |
      | object... args         | sql语句值                                                    |

      ```java
      //查询返回集合
          @Override
          public List<Book> findALlBook() {
              String sql = "select * from t_book";
              List<Book> bookList = jdbcTemplate.query(sql, new BeanPropertyRowMapper<Book>(Book.class));
              return bookList;
          }
      ```

   3. 测试

      ```java
      //查询返回集合
              List<Book> all = bookService.findAll();
              System.out.println(all);
      ```

##### 五、批量操作

1. 批量操作：操作表里面多条记录

2. jdbcTemplate实现批量添加的操作

   batchUpdate(String sql,List<Object[]> barchArgs)

   | 参数                     | 说明                       |
   | ------------------------ | -------------------------- |
   | String SQL               | sql语句                    |
   | List<object[]> barchArgs | list集合添加的多条记录数据 |
   
   ```java
    //批量添加
       @Override
       public void batchAddBook(List<Object[]> bachArgs) {
           String sql = "insert into t_book values(?,?,?)";
           int[] ints = jdbcTemplate.batchUpdate(sql, bachArgs);
           System.out.println(Arrays.toString(ints));
       }
   ```
   
3. 测试

   ```java
   //批量添加
           List<Object[]> list = new ArrayList<>();
           Object[] o1 = {"4","张三","qwe"};
           Object[] o2 = {"5","李四","qwerty"};
           Object[] o3 = {"6","王五","ewq"};
           list.add(o1);
           list.add(o2);
           list.add(o3);
           bookService.batchAdd(list);
   ```

4. jdbcTemplate实现批量修改的操作

   ```java
    @Override
       public void batchUpdateBook(List<Object[]> bachArgs) {
           String sql = "update t_book set username=?,ustatus=? where user_id=?";
           int[] ints = jdbcTemplate.batchUpdate(sql, bachArgs);
           System.out.println(Arrays.toString(ints));
       }
   ```

5. 测试

   ```java
   //批量修改
           List<Object[]> list = new ArrayList<>();
           Object[] o1 = {"红楼梦","aaa","1"};
           Object[] o2 = {"西游记","bbb","2"};
           Object[] o3 = {"水浒传","ccc","3"};
           list.add(o1);
           list.add(o2);
           list.add(o3);
           bookService.batchUpdate(list);
   ```

6. 批量删除

   ```Java
    //批量删除
       @Override
       public void batchDeleteBook(List<Object[]> bachArgs) {
           String sql = "delete from t_book where user_id=?";
           int[] ints = jdbcTemplate.batchUpdate(sql, bachArgs);
           System.out.println(ints);
       }
   ```

7. 测试

   ```java
   //批量删除
           List<Object[]> list = new ArrayList<>();
           Object[] o1 = {"4"};
           Object[] o2 = {"5"};
           list.add(o1);
           list.add(o2);
           bookService.batchUpdate(list);
   ```

## 五、事务管理

#### 一、概念

1. 事务是数据库操作的最基本单元，逻辑上一组操作，要么都成功，要么失败一个其他所有操作都是失败
2. 典型场景：银行转账
3. 事务四个特性(ACID)
   1. 原子性
   2. 一致性
   3. 隔离性
   4. 持久性

#### 二、事务操作

##### 1.搭建事务操作环境

   1. 创建数据库表，添加记录

   2. 创建service，创建dao，完成对象创建和注入关系

      service注入dao，在dao注入jdbcTemplate，在jdbcTemplate注入DataSource

      ```java
      @Service
      public class UserService {
          //注入dao
          @Autowired
          private UserDao userDao;
      }
      ```

      ```java
      @Repository
      public class UserDaoImpl implements UserDao {
          @Autowired
          private JdbcTemplate jdbcTemplate;
      }
      ```

   3. 在dao创建两个方法：多钱和少钱方法，在service创建方法(转账的方法)

      ```java
      @Override
          public void addMoney() {
              String sql = "update t_account set money=money+? where username=?";
              jdbcTemplate.update(sql, 100, "李四");
          }
          @Override
          public void reduceMoney() {
              String sql = "update t_account set money=money-? where username=?";
              jdbcTemplate.update(sql, 100, "张三");
          }
      ```

      ```java
      //转账方法
          public void accountMoney() {
              userDao.reduceMoney();
              userDao.addMoney();
          }
      ```

   4. 上面代码，正常执行没有问题，但是代码如果产生异常，会有问题

      问题：张三扣除100块，而李四并没有收到100块

      以上问题问题解决方案，使用事务进行解决，要么一起成功要么一起失败

      > 事务的操作过程：
      >
      > 1. 开启事务
      > 2. 进行业务操作(try~catch捕获并处理异常)
      > 3. 没有异常，提交事务
      > 4. 出现了异常，事务回滚

##### 2.事务操作(spring事务管理介绍)

   1. 事务添加到JavaEE三层结构里面Service层(业务逻辑层)

   2. 在Spring进行事务管理操作

      > 有两种方式：编程式事务管理和声明式事务管理(使用)

   3. 声明式事务管理

      1. 基于注解方式
      2. 基于xml配置文件方式

   4. 在Spring进行声明式事务管理，底层使用AOP原理

   5. Spring事务管理API

      1. 提供了一个接口，代表事务管理器，这个接口针对不同框架提供不同的实现类

         `PlatformTransactionManager`

##### 3.事务操作(注解声明式事务管理)

   1. 在Spring配置文件配置事务管理器

      ```xml
      <!--    创建事务管理器-->
          <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
              <!--        注入数据源-->
              <property name="dataSource" ref="database"></property>
          </bean>
      ```

   2. 在Spring配置文件,开启事务注解

      在spring配置文件引入名称空间tx

      ```xml
      <!--    开启事务注解-->
          <tx:annotation-driven transaction-manager="transactionManager"></tx:annotation-driven>
      ```

   3. 在service类上面添加事务注解

      @Transactional,这个注解添加到类上面,也可以添加到方法上面

      添加在类上,表示这个类中所有方法都添加了事务

      添加到方法上,只为该方法提供事务

      ```java
      @Service
      @Transactional
      public class UserService {
      ```

##### 4.事务操作(声明式事务管理参数)

   | 参数名                    | 说明           |
   | ------------------------- | -------------- |
   | Propagation propagation() | 事务的传播行为 |
   | Isolation isolation()     | 事务的隔离级别 |
   | int timeout()             | 事务的超时时间 |
   | boolearn readOnly         | 是否只读       |
   | rollbackFor               | 回滚           |
   | noRollbackFor             | 不回滚         |

   **`propagation()`**:事务的传播行为

   | 参数          | 说明                                                         |
   | ------------- | ------------------------------------------------------------ |
   | MANDATORY     | 支持当前事务，如果不存在则引发异常。                         |
   | NESTED        | 如果当前事务存在，则在嵌套事务内执行，否则行为类似于 `REQUIRED` |
   | NEVER         | 以非事务方式执行，如果存在事务则引发异常。                   |
   | NOT_SUPPORTED | 以非事务方式执行，如果存在当前事务，则挂起当前事务。         |
   | REQUIRED      | 支持当前事务，如果不存在则创建新事务。                       |
   | REQUIRES_NEW  | 创建一个新事务，如果当前事务存在，则挂起当前事务。           |
   | SUPPORTS      | 支持当前事务，如果不存在，则以非事务方式执行。               |

   **`isolation`**:事务的隔离级别

   | 参数             | 说明                                                      |
   | ---------------- | --------------------------------------------------------- |
   | DEFAULT          | 使用基础数据存储区的默认隔离级别。                        |
   | READ_COMMITTED   | 一个常量，指示阻止脏读;可能发生不可重复的读取和幻像读取。 |
   | READ_UNCOMMITTED | 一个常量，指示可能发生脏读、不可重复读和幻像读。          |
   | REPEATABLF_READ  | 一个常量，指示阻止脏读取和不可重复读取;可能发生幻象读取   |
   | SERIALZABLE      | 一个常量，指示阻止脏读、不可重复读和幻像读。              |

##### 5.事务操作(xml声明式事务管理)

   1. 在spring配置文件中进行配置

      第一步：配置事务管理器

      第二步：配置通知

      第三步：配置切入点和切面

      ```xml
       <!--    创建事务管理器-->
          <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
              <!--        注入数据源-->
              <property name="dataSource" ref="database"></property>
          </bean>
          <!--    配置通知-->
          <tx:advice id="txadvice">
              <!--        配置事务参数-->
              <tx:attributes>
                  <!--            指定那种规则的方法上面添加事务-->
                  <tx:method name="accountMoney" propagation="REQUIRED"/>
                  <!--            <tx:method name="account*"/>-->
              </tx:attributes>
          </tx:advice>
          <!--    配置切入点和切面-->
          <aop:config>
              <!--        配置切入点-->
              <aop:pointcut id="pt" expression="execution(* com.test.spring5.service.UserService.*(..))"/>
              <!--        配置切面-->
              <aop:advisor advice-ref="txadvice" pointcut-ref="pt"/>
          </aop:config>
      ```







