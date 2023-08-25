---
title: SSM框架开发项目
date: 2023-03-15 11:18:57
tags: 框架
categories: 框架
cover: https://w.wallhaven.cc/full/85/wallhaven-85gw6k.jpg 
---

# SSM

## Spring

1. **IOC**

   Inverse of Control（控制反转）指把创建对象过程交给Spring进行管理

   1.1 依赖注入

   依赖注入实现了控制反转的思想

   依赖注入常见的实现方式包括两种：

   ​	第一种：Set注入

   ​	第二种：构造注入

   1.2 基于XML管理Bean

   ​	1.2.1获取bean

     1. 根据id获取

        由于id属性指定了bean的唯一标识，所以根据bean标签的id属性可以精确获取到一个组件对象

     2. 根据类型获取

        ```java
        @Test
        public void testHelloWorld(){
        	ApplicationContext ac = new ClassPathXmlApplicationContext("bean.xml");
        	HelloWorld bean = ac.getBean(HelloWorld.class);
        	bean.sayHello();
        }
        ```

        

     3. 根据id和类型

   ```java
   @Test
   public void testHelloWorld(){
   	ApplicationContext ac = new ClassPathXmlApplicationContext("bean.xml");
   	HelloWorld bean = ac.getBean("helloworld",HelloWorld.class);
   	bean.sayHello();
   }
   ```

   bean的作用域

   | 取值            | 含义                                                      |
   | --------------- | --------------------------------------------------------- |
   | singleton(默认) | 在IOC容器中，这个bean的对象始终为单实例，容器初始化时创建 |
   | prototype       | 这个bean在容器中有多个实例，获取bean时创建                |

   

2. **AOP**

​	Aspect Oriented Programming（面向切面编程）AOP用来封装多个类的公共行为，将那些与业务无关，却为业务模块所共同调用的逻辑封装起来，减少系统的重复代码，降低模块间的耦合度。另外，Aop还解决一些系统层面上的问题比如日志，事务、权限等





1. Bean的生命周期

   > bean对象创建（调用无参数构造）
   >
   > 给bean对象设置相关属性
   >
   > bean后置处理器（初始化之前）
   >
   > bean对象初始化（调用指定初始化方法）
   >
   > bean后置处理器（初始化之后）
   >
   > bean对象创建完成了，可以使用了
   >
   > bean对象销毁（配置指定销毁的方法）
   >
   > IOC容器关闭

   

2. 自动装配

   > 根据指定的策略，在IOC容器中匹配某一个bean，自动为指定bean中所依赖的类类型或接口类型属性赋值
   >
   > 例如：注意autowire属性的配置
   >
   > > ```java
   > > <bean 
   > >    id="userController"
   > >    class="com.spring6.iocxml.auto.controller.UserController"
   > >    autowire="byType"></bean>
   >     
   > byName：必须保证属性名称和bean的id名称保持一致

3. 集合注入

4. 声明式事务处理

5. Spring注解

   它是一种特殊标记，可以在编译、类加载和运行时被读取，执行相应的处理，开发人员可以通过注解在不改变原有代码和逻辑的情况下，在源代码中嵌入补充信息。

   > Spring 通过注解实现自动装配的步骤如下：
   >
   > 1. 引入依赖
   > 2. 开始组件扫描
   > 3. 使用注解定义Bean
   > 4. 依赖注入

| 注解        | 说明                                                         |
| ----------- | ------------------------------------------------------------ |
| @Component  | 该注解用于描述Spring中的Bean，他是一个泛化的概念，仅仅表示容器中的一个组件（Bean），并且可以作用在应用的任何层次，例如Service层、Dao层等。使用时只需将该注解标注在相应类上即可。 |
| @Repository | 该注解用于将数据访问层（Dao层）的类标识为Spring中的Bean，其功能与@Component相同 |
| @Service    | 该注解通常作用在业务层（Service层），用于将业务层的类标识为Spring中的Bean，其功能与@Component相同 |
| @Controller | 该注解通常作用在控制层（如SpringMVC的Controller），用于将控制层的类标识为Spring中的Bean，其功能与@Component相同 |



## SpringMVC

1. handlerMapping

2. RequestMapping

3. springMVC参数传递

4. 适配器

5. 拦截器

6. 试图和模型

7. 上传文件

8. 请求方式

   > PUT

   > POST

   > GET

   > DELETE



## MyBatis

#### 一、概念

1. 为什么要用ORM
2. 为什么使用MyBatis
3. Mybatis快速入门

#### 二、进阶

1. MyBatis的配置
2. 映射器的配置
3. 动态SQL
4. 整合Spring
5. 缓存
6. 关联查询
7. 多对多查询
8. 代码生成器



#### 三、高级

1. 源码分析
2. 插件开发
3. 分页插件使用与源码分析