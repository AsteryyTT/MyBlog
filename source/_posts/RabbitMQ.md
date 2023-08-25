---
title: RabbitMQ消息队列
date: 2023-06-18 11:18:57
tags: 中间件
categories: 中间件
cover: /img/images/Rabbit.png
---

# RabbitMQ

### 1.什么是MQ

​	MQ(message queue),字面意思，本质上是一个队列，FIFO先进先出，只不过队列中存储的是消息，还是一种跨进程通信的机制，用于上下游传递消息，MQ是一种非常常见的上下游`逻辑解耦+物理解耦`的消息通信服务。使用了MQ之后，消息发送上游只需要依赖MQ，不用依赖其他服务。

#### 1.1为什么要用MQ

1. **流量消峰**

   流量削峰，如果观看抽奖或秒杀系统的请求监控曲线，你就会发现这类系统在活动开放的时间段内会出现一个波峰，而在活动未开放时，系统的请求量、机器负载一般都是比较平稳的。为了节省机器资源，我们不可能时时都提供最大化的资源能力来支持短时间的高峰请求。所以需要使用一些技术手段，来削弱瞬时的请求高峰，让系统吞吐量在高峰请求下保持可控

   **例子**：有一个订单系统，最多处理一万次订单，这个处理能力应付正常时段的下单绰绰有余，正常下单一秒就有返回结果，但是在高峰期，如果有两万次下单操作系统处理不过来，只能限制订单超过一万不允许用户下单，使用消息队列做缓冲，我们可以取消这个限制，吧一秒内下的订单分散成一段时间来处理，这时有些用户下单后十几秒后才有下单成功的结果，导致用户体验不佳。

   `如果请求过多，超过服务器压力点，会导致宕机,MQ就是让高峰期的请求数达到一个可控范围，使服务器保持稳定，不会宕机`

2. **应用解耦**

   模块之间的调用十分复杂，为了降低模块与模块之间调用依赖，使用MQ将两系统分开,不直接调用系统接口,减轻两系统依赖关系

   **例子**：电商应用为例，应用中有订单系统，库存系统，物流系统，支付系统，用户创建订单后，如果耦合调用库存系统，物流系统，支付系统，任何一个子系统出现故障，都会造成下单操作异常，当转变成基于消息队列的方式后，系统调用的问题会减少很多比如物流系统因为发送故障，需要几分钟来修复，在这几分钟的时间里，物流系统要处理的内存被缓存在消息队列中，用户的下单操作可以正常完成，当物流系统恢复后，继续处理订单信息即可，订单用户感受不到物流系统的故障，提升系统的可用性。

3. **异步处理**

   异步指一个执行中的任务，拆开成多个子任务各自执行。异步与同步处理相对，同步指多个执行中的子任务，等待所有子任务执行完再继续执行。异步的作用是使有I/O操作的任务最大化利用处理器的计算，以达到缩短任务的完成时间。

### 2.RabbitMQ概念

​	RabbitMQ是一个消息中间件：它接受并转发消息，你可以把它当做一个传递点，当你要发送一个包裹时，你把你的包裹放到快递站，快递员最终会把你的快递送到收件人哪里，按照这种逻辑RabbitMQ是一个快递站，一个快递员帮你传递快件，它不处理快件，而是接收，存储，转发消息数据

#### 2.1四大核心概念

1. 生产者

   产生数据发送消息的程序是生产者

2. 交换机

   交换机是RabbitMQ非常重要的一个部件，一方面它接收来自生产者的消息，另一方面他将消息推送到队列中，交换机必须确切知道如何处理它接收到的消息，是将这些消息推送到特定队列还是推送到多个队列，亦或者吧消息丢弃，这个得有交换机类型决定

3. 队列

   队列是RabbitMQ内部使用得一种数据结构，尽管消息流经RabbitMQ和应用程序，但他们只能存储在队列中，队列受主机得内存和磁盘限制和约束，本质上是一个大得消息缓冲区，许多生产者可以将消息发送到一个队列，许多消费者可以尝试从一个队列接收数据，这就是我们使用队列得方式

4. 消费者

   消费与接收具有相似得含义。消费者大多时候是一个等待接收消息得程序，请注意生产者，消费者和消息中间件很多时候并不在同一机器上，同一个应用程序既可以是生产者又是可以是消费者。

3.RabbitMQ核心部分

1. Hello World(简单模式)
2. Work queues(工作模式)
3. Publish/Subscribe(发布订阅模式)
4. Routing(路由模式)
5. Topics(主题模式)
6. Publisher Confirms(发布确认模式)

### 3.安装RabbitMQ

RbbitMQ官网：https://www.rabbitmq.com/

RabbitMQ及环境下载：https://packagecloud.io/rabbitmq/

1. 安装Erlang环境

```linux
rpm -ivh erlang-xx.x-x.linux版本.x86_64.rpm
```

2. 安装socat依赖

```
yum install socat -y
```

3. 安装rabbitMQ-server

```
rpm -ivh rabbitmq-server-x.x.x-linux版本.noarch.rpm
```

#### 3.1**常用命令**

1. 添加开机启动RabbitMQ服务

   ```
   chkconfig rabbitmq-server on
   ```

2. 启动RabbitMQ服务

   ```
   /sbin/service rabbitmq-server start
   ```

3. 查看RabbitMQ服务状态

   ```
   /sbin/service rabbitmq-server status
   ```

4. 停止RabbitMQ服务状态

   ```
   /sbin/service rabbitmq-server stop
   ```

5. 开启Web管理插件

   ```
   rabbitmq-plugins enable rabbitmq_management
   
   用默认账户密码(guest)访问地址http：//安装rabbitmq的主机ip地址:15672
   这里会出现一个权限问题
   ```

   > 注意：15672端口必须开启或者关闭防火墙否则无法访问,正式项目不建议关闭防火墙
   >
   > systemctl stop firewalld  
   >
   > systemctl enabled firewalld
   >
   > systemctl start firewalld
   >
   > systemctl status firewalld

6. 查看RabbitMQ有那些用户

   ```
   rabbitmqctl list_users
   ```

7. 添加一个新的用户

   1. 创建账号

      > rabbitmqctl add_user 用户名 密码

   2. 设置用户角色

      > rabbitmqctl set_user_tags 用户名 权限
      >
      > 例：rabbitmqctl set_user_tags user administrator

   3. 设置用户权限

      > set_permissions [-p <vhostpath>] <user> <conf> <write> <read>
      >
      > 例如：rabbitmq set_permissions -p"/" admin ".*" ".*"".*"
      >
      > 用户user_admin具有/vhost1这个virtual host中所有资源的配置、写、读权限

   4. 修改密码

      > rabbitmqctl  change_password  用户名  '新密码'
      >
      > 例：rabbitmqctl  change_password  admin  'root'

### 4.Hello World(简单模式)

#### 4.1创建maven项目

#### 4.2导入依赖

> <!--        rabbitmq依赖客户端-->
>
> <dependency>    
>
> ​	<groupId>com.rabbitmq</groupId>    
>
> ​	<artifactId>amqp-client</artifactId>    
>
> ​	<version>5.18.0</version>
>
> </dependency>
>
> <!--        操作文件流依赖-->
>
> <dependency>    
>
> ​	<groupId>commons-io</groupId>    
>
> ​	<artifactId>commons-io</artifactId>    
>
> ​	<version>2.11.0</version>
>
> </dependency>

#### 4.3构建代码

​	构建一个生产者P端，消费者C端，通过P端发消息给队列传递给C端，这个队列采用RabbitMQ来保留消息缓冲区

##### 4.3.1构建生产者

```java
/**
 * 生产者
 * 生产者：发送消息
 */
public class Producer {
    //队列名称
    public static final String QUEUE_NAME = "hello";
    //发消息
    public static void main(String[] args) throws Exception {
        //创建一个连接工厂
        ConnectionFactory factory = new ConnectionFactory();
        //设置工厂ip,连接RabbitMQ的队列
        factory.setHost("192.168.100.137");
        //设置用户名和密码
        factory.setUsername("admin");
        factory.setPassword("root");
        //创建连接  此处需要处理一个异常
        Connection connection = factory.newConnection();
        //获取信道
        Channel channel = connection.createChannel();
        /**
         * 生成一个队列
         * @param String queue,                 队列名称
         * @param boolean durable               是否保存消息,队列里的消息是否持久化，默认队列里的消息存储在内存中
         * @param boolean exclusive,            该队列是否只对一个消费者提供消费， 是否进行消息的共享，true可以多个消费者消费，默认情况下false：只能一个消费者
         * @param boolean autoDelete            是否自动删除，最后一个消费者断开连接后该队列是否自动删除
         * @param Map<String, Object> arguments 其他参数，如死信消息、延迟消息、
         */
        channel.queueDeclare(QUEUE_NAME, false, false, false, null);
        //发送消息
        String message = "hello world";
        //利用信道发布消息
        /**
         *@param String exchange            交换机   默认交换机，可以忽略
         * @param String routingKey         路由key   填写队列名称
         * @param BasicProperties props     参数      无，填写null
         * @param byte[] body               消息体
         */
        channel.basicPublish("", QUEUE_NAME, null, message.getBytes());
        System.out.println("发送完毕");
    }
}
```

> 注意：还需要开放一个5672客户端端口，并且设置当前用户Permissions权限

##### 4.3.1构建消费者

```java
//消费者
//接收消息
public class Consumer {
    //队列名称   接收此队列消息
    public static final String QUEUE_NAME = "hello";
    //接收消息
    public static void main(String[] args) throws Exception {
        //创建连接工厂
        ConnectionFactory factory = new ConnectionFactory();
        factory.setHost("192.168.100.137");
        factory.setUsername("admin");
        factory.setPassword("root");
        Connection connection = factory.newConnection();
        //建立信道  利用信道接收消息
        Channel channel = connection.createChannel();
        //声明  接收消息
        DeliverCallback deliverCallback = (consumerTag, message) -> {
            System.out.println("消息体：" + new String(message.getBody()));
        };
        //声明  取消消息
        CancelCallback cancelCallback = (consumerTag) -> System.out.println("消费消息被中断时执行");
        // 消费者，接收消息
        /**
         * @parma String queue                      队列名
         * @parma boolean autoAck                   是否自动应答   消费成功后是否自动应答
         * @parma DeliverCallback deliverCallback   消息的回调     消息未成功消费的回调
         * @parma CancelCallback cancelCallback     消息的回调     消费者取消消息的回调
         */
        channel.basicConsume(QUEUE_NAME, true, deliverCallback, cancelCallback);
    }
}
```

### 5.Work Queues(工作模式)

​	工作队列(任务队列)的主要思想是避免立即执行资源密集型任务。而不得不等待它完成。相反我们安排任务在之后执行，我们把任务封装为消息并将其发送到队列，在后台运行的工作进程将弹出任务并最终稿执行作业，当有多个工作线程时，这些工作线程将一起处理这些任务。

#### 5.1轮询分发消息

1. 抽取工具类

   ```java
   //rabbitmq创建连接信道
   public class RabbitMQUtils {
       //得到一个连接信道
       public static Channel getChannel() throws Exception {
           ConnectionFactory factory = new ConnectionFactory();
           factory.setHost("192.168.100.137");
           factory.setUsername("admin");
           factory.setPassword("root");
           Connection connection = factory.newConnection();
           Channel channel = connection.createChannel();
           return channel;
       }
   }
   ```
   
2. 消费者代码(接收消息的工作线程)

   ```java
   //工作线程01
   public class Work01 {
       //队列名称
       public static final String QUEUE_NAME = "hello";
       //接收消息
       public static void main(String[] args) throws Exception {
   
           Channel channel = RabbitMQUtils.getChannel();
           //消息接收
           DeliverCallback deliverCallback = (consumerTag, message) -> System.out.println(new String(message.getBody()));
           //消息取消
           CancelCallback cancelCallback = (consumerTag) -> System.out.println("消息被消费者取消：" + consumerTag);
           //消息的接收
           System.out.println("C1线程等待中...");
           channel.basicConsume(QUEUE_NAME, true, deliverCallback, cancelCallback);
       }
   }
   ```

3. 生产者代码(发送消息)

   ```java
   public class Task01 {
       //队列名称
       public static final String QUEUE_NAME = "hello";
       public static void main(String[] args) throws Exception {
           Channel channel = RabbitMQUtils.getChannel();
           //声明队列
           channel.queueDeclare(QUEUE_NAME, false, false, false, null);
           //从控制台当中接收信息
           Scanner scanner = new Scanner(System.in);
           while (scanner.hasNext()) {
               String message = scanner.next();
               channel.basicPublish("", QUEUE_NAME, null, message.getBytes());
               System.out.println("发送消息完成：" + message);
           }
       }
   }
   ```

#### 5.2消息应答

1. 消息应答概念

   消费者完成一个任务可能需要一段时间，如果其中一个消费者处理一个长的任务并仅只完成了部分突然挂掉了，RabbitMQ一旦向消费者传递了一条消息，便立即将该消息标记为删除。在这种情况下，突然有个消费者挂掉了，我们将丢失正在处理的消息。以及后续发送给该消费者的消息，因它无法接收到。

   `为了保证消息在发送过程中不丢失，rabbitMQ引入消息应答机制，消息应答就是：消费者在接收到消息并且处理该消息之后，告诉rabbitMQ它已经处理了，RabbitMQ可以把该消息删除了。`

2. 自动应答

   消息发送后立即被认为已经传送成功，这种模式需要在高吞吐量和数据传输安全性方面做权衡，因为这种模式如果消息在接收到之前，消费者那边出现连接或者channel关闭，那么消息就丢失了，当然另一方面这种模式消费者那边可以传递过载的消息，没有对传递消息的数量进行限制，当然这样有可能使得消费者这把由于接收太多还来不及处理的消息，导致这些消息的积压，最终使得内存耗尽，最终这些消费者线程被操作系统杀死，所以这种模式仅适用在消费者可以高效并以某种速率能够处理这样的消息的情况下使用

3. 消息应答的方法

   A.Channel.basicAck(用于肯定确认)

   ​	RabbitMQ已经知道该消息并且成功处理的消息，可以将其丢弃了

   B.Channel.basicNack(用于否定确认)

   C.Channel.basicReject(用于否定确认)

   ​	与Channel.basicNack相比少一个参数(批量处理)，不处理该消息了直接拒绝，可以将其丢弃

4. Multiple的解释

   手动应答的好处就是可以批量应答并且减少网络拥堵

   ```java
   channel.basicAck(deliveryTag,true);
   ```

   multiple的true和false代表不同意思

   true：代表批量应答channel上未应答的消息

   例：channel上有传送tag的消息5，6，7，8当前tag是8那么此时5-8的这些还未应答的消息都会被确认收到消息的应答

   false：如true相反。一对一应答

5. 消息自动重新入队

   如果消费者由于某些原因丢失连接(其通道已关闭，连接已关闭或TCP连接丢失)，导致消息未发送ACK确认，RabbitMQ将了解到消息未完全处理，并将对列重新排队，如果此时其他消费者可以处理，它将很快将其重新分发给另一个消费者，这样，即使某个消费者偶尔死亡，也可以确保不会丢失任何消息

6. 消息手动应答(代码)

   默认采用的是自动应答，所以我们要想实现消息消费过程中不丢失，需要把自动应答改为手动应答

   ```java
   //消息手动应答并不丢失，并且放回消息队列
   public class Task02 {
       private static final String QUEUE_NAME = "ack_queue";
   
       public static void main(String[] args) throws Exception {
           Channel channel = RabbitMQUtils.getChannel();
           channel.queueDeclare(QUEUE_NAME, false, false, false, null);
           Scanner scanner = new Scanner(System.in);
           while (scanner.hasNext()) {
               String message = scanner.next();
               channel.basicPublish("", QUEUE_NAME, null, message.getBytes("UTF-8"));
               System.out.println("生产者发送消息：" + message);
           }
       }
   }
   ```

   ```java
   public class Worker01 {
       private static final String QUEUE_NAME = "ack_queue";
   
       public static void main(String[] args) throws Exception {
           Channel channel = RabbitMQUtils.getChannel();
           System.out.println("C1等待接收消息处理，短时间处理");
           DeliverCallback deliverCallback = ((consumerTag, message) -> {
               //沉睡一秒
               try {
                   Thread.sleep(1000);
               } catch (InterruptedException e) {
                   throw new RuntimeException(e);
               }
               System.out.println("接收到的消息：" + new String(message.getBody(), "UTF-8"));
               //手动应答
               /**
                * @param long deliveryTag     消息的标识
                * @param boolean multiple     是否批量应答
                */
               channel.basicAck(message.getEnvelope().getDeliveryTag(), false);
           });
           boolean autoAck = false;
           channel.basicConsume(QUEUE_NAME, autoAck, deliverCallback, ((consumerTag, message) -> System.out.println(consumerTag + "消费者取消消费接口回调逻辑")));
       }
   }
   ```

   ```java
   public class Worker02 {
       private static final String QUEUE_NAME = "ack_queue";
   
       public static void main(String[] args) throws Exception {
           Channel channel = RabbitMQUtils.getChannel();
           System.out.println("C2等待接收消息处理，长时间处理");
           DeliverCallback deliverCallback = ((consumerTag, message) -> {
               //沉睡一秒
               try {
                   Thread.sleep(30000);
               } catch (InterruptedException e) {
                   throw new RuntimeException(e);
               }
               System.out.println("接收到的消息：" + new String(message.getBody(), "UTF-8"));
               //手动应答
               /**
                * @param long deliveryTag     消息的标识
                * @param boolean multiple     是否批量应答
                */
               channel.basicAck(message.getEnvelope().getDeliveryTag(), false);
           });
           boolean autoAck = false;
           channel.basicConsume(QUEUE_NAME, autoAck, deliverCallback, ((consumerTag, message) -> System.out.println(consumerTag + "消费者取消消费接口回调逻辑")));
       }
   }
   ```

#### 5.3RabbitMQ持久化

1. ##### 概念

   如果保证当RabbitMQ服务突然停掉以后消息生产者发送过来的消息不丢失。默认情况下RabbitMQ退出或由于某种原因崩溃了，它忽视队列和消息，除非告知它不要这么做，确保消息不会丢失需要做两件事`我们需要将队列和消息都标记为持久化`

2. ##### 队列如何实现持久化

   RabbitMQ中的队列默认是非持久化的，RabbitMQ如果重启，该队列就会被删除掉，如果要队列实现持久化，需要声明队列的时候把durable参数设置为持久化

   ```java
    channel.queueDeclare(QUEUE_NAME, `true持久化，false非持久化`, false, false, null);
   ```

   > 注意：如果之前声明队列不是持久化，需要把原先队列删除，或者重新创建一个新的持久化队列，不然会出错
   >
   > `ITION_FAILED - inequivalent arg 'durable' for queue 'task_queue' in vhost '/': received 'true' but current is 'false'`

3. ##### 消息实现持久化

   消息持久化需要在消息生产者中添加属性，`MessageProperties.PERSISTENT_TEXT_PLAIN`

   ```java
   channel.basicPublish("", QUEUE_NAME, MessageProperties.PERSISTENT_TEXT_PLAIN, message.getBytes("UTF-8"));
   ```

   > 将消息持久化并不能完全保证消息不丢失，尽管它告诉RabbitMQ将消息保存在磁盘，但是这里依然存在当消息刚准备存储在磁盘的时候，但是还没有存储完，消息还在缓存的一个间隔点，此时并没有真正写入磁盘，持久性保证并不强，但是对于简单的任务队列来说绰绰有余，如果需要更强的持久化策略，可以用发布确认

4. ##### 不公平分发

   能者多劳，一台机器处理快，一台机器处理慢，轮询分发会导致处理快的机器有一个空闲期等待处理慢的机器处理完毕，为了提高利用每台机器的最高性能，建议使用不公平分发，谁处理的快谁就多处理一点，处理慢的就少处理点

   `为了避免这种情况，创建信道设置参数channel.basicQos(1);默认为轮询分发`

5. ##### 预取值

   本身消息的发送就是异步发送的，所以在任何时候，channel 上肯定不止只有一个消息另外来自消费者的手动确认本质上也是异步的。因此这里就存在一个未确认的消息缓冲区，因此希望开发人员能`限制此缓冲区的大小，以避免缓冲区里面无限制的未确认消息问题。`这个时候就可以通过使用 basic.qos 方法设置“预取计数”值来完成的。`该值定义通道上允许的未确认消息的最大数量`。一旦数量达到配置的数量RabbitMO 将停止在通道上传递更多消息，除非至少有一个未处理的消息被确认，例如，假设在通道上有未确认的消息 5、6、7，8，并且通道的预取计数设置为 4，此时 RabbitMQ 将不会在该通道上再传递任何消息，除非至少有一个未应答的消息被 ack。比方说 tag=6 这个消息刚刚被确认 ACK，RabbitMQ.将会感知这个情况到并再发送一条消息。消息应答和 QoS 预取值对用户吞吐量有重大影响。通常，增加预取将提高向消费者传递消息的速度。`虽然自动应答传输消息速率是最佳的，但是，在这种情况下已传递但尚未处理的消息的数量也会增加，从而增加了消费者的 RAM 消耗`(随机存取存诸器)应该小心使用具有无限预处理的自动确认模式或手动确认模式，消费者消费了大量的消息如果没有确认的话，会导致消费者连接节点的内存消耗变大，所以找到合适的预取值是一个反复试验的过程，不同的负载该值取值也不同 100 到 300 范围内的值通常可提供最佳的吞吐量，并目不会给消费者带来太大的风险。预取值为 1 是最保守的。当然这将使吞吐量变得很低，特别是消费者连接延迟很严重的情况下，特别是在消费者连接等待时间较长的环境中，对于大多数来说，稍微高一点的值将是最佳的
   
### 6.发布确认

#### 6.1发布确认原理

​    生产者将信道设置成 confirm 模式，一旦信道进入 confirm 模式，所有在该信道上面发布的消息都将会被指派一个唯一的 ID(从1 开始)，一旦消息被投递到所有匹配的队列之后，broker就会发送一个确认给生产者(包含消息的唯一 ID)，这就使得生产者知道消息已经正确到达目的队列了，如果消息和队列是可持久化的，那么确认消息会在将消息写入磁盘之后发出，broker 回传给生产者的确认消息中 delivery-tag 域包含了确认消息的序列号，此外 broker 也可以设置basic.ack 的 multiple 域，表示到这个序列号之前的所有消息都已经得到了处理。

​    confirm 模式最大的好处在于他是异步的，一旦发布一条消息，生产者应用程序就可以在等信道返回确认的同时继续发送下一条消息，当消息最终得到确认之后，生产者应用便可以通过回调方法来处理该确认消息，如果 RabbitMQ 因为自身内部错误导致消息丢失，就会发送一条 nack 消息，生产者应用程序同样可以在回调方法中处理该 nack 消息。

#### 6.2发布确认的策略

1. ##### 开启发布确认的方法

   发布确认默认是没有开启的，如果要开启需要调用方法confirmSelect，每当你要想使用发布确认，都需要在channel上调用该方法

   ```
   Channel channel = connection.createChannel();
   channel.confirmSelect();
   ```

2. ##### 单个确认发布

   这是一种简单的确认方式，它是一种同步确认发布的方式，也就是发布一个消息之后只有它被确认发布，后续的消息才能继续发布,waitForConfirmsOrDie(long)这个方法只有在消息被确认的时候才返回，如果在指定时间范围内这个消息没有被确认那么它将抛出异常。
   这种确认方式有一个最大的缺点就是:发布速度特别的慢，因为如果没有确认发布的消息就会阻塞所有后续消息的发布，这种方式最多提供每秒不超过数百条发布消息的吞吐量。当然对于某些应用程序来说这可能已经足够了。

   ```java
    public static void publishMessageIndividually() throws Exception {
           Channel channel = RabbitMQUtils.getChannel();
           String QUEUE_NAME = UUID.randomUUID().toString();
           channel.queueDeclare(QUEUE_NAME, false, false, false, null);
           //开启发布确认
           channel.confirmSelect();
           //开始时间
           long startTime = System.currentTimeMillis();
   
           for (int i = 0; i < 1000; i++) {
               String message = "单个确认" + i;
               channel.basicPublish("", QUEUE_NAME, null, message.getBytes());
               boolean flag = channel.waitForConfirms();
               if (flag) {
                   System.out.println("消息 " + i + " 发送成功");
               }
           }
           //结束时间
           long stopTime = System.currentTimeMillis();
           System.out.println("发布单个确认消息耗时：" + (stopTime - startTime) + "ms");
       }
   ```

3. ##### 批量确认发布

   上面那种方式非常慢，与单个等待确认消息相比，先发布一批消息然后一起确认可以极大地提高吞吐量，当然这种方式的缺点就是:当发生故障导致发布出现问题时，不知道是哪个消息出现问题了，我们必须将整个批处理保存在内存中，以记录重要的信息而后重新发布消息。当然这种方案仍然是同步的，也一样阻塞消息的发布。

   ```java
   public static void publishMessageBatch() throws Exception {
           Channel channel = RabbitMQUtils.getChannel();
           String QUEUE_NAME = UUID.randomUUID().toString();
           channel.queueDeclare(QUEUE_NAME, false, false, false, null);
           //开启发布确认
           channel.confirmSelect();
   
           //批量确认消息大小
           int batchSize = 100;
   
           //开始时间
           long startTime = System.currentTimeMillis();
           for (int i = 0; i < 1000; i++) {
               String message = "单个确认" + i;
               channel.basicPublish("", QUEUE_NAME, null, message.getBytes());
               //判断是否达到消息大小
               if (i % batchSize == 0)
                   //发布确认
                   channel.waitForConfirms();
           }
           //结束时间
           long stopTime = System.currentTimeMillis();
           System.out.println("发布批量确认消息耗时：" + (stopTime - startTime) + "ms");
       }
   ```

4. ##### 异步确认发布

   异步确认虽然编程逻辑比上两个要复杂，但是性价比最高，无论是可靠性还是效率都没得说他是利用回调函数来达到消息可靠性传递的,这个中间件也是通过函数回调来保证是否投递成功，下面就让我们来详细讲解异步确认是怎么实现的。	

   ```java
    //异步确认消息
       public static void publishMessageAsync() throws Exception {
           Channel channel = RabbitMQUtils.getChannel();
           String QUEUE_NAME = UUID.randomUUID().toString();
           channel.queueDeclare(QUEUE_NAME, false, false, false, null);
           //开启发布确认
           channel.confirmSelect();
           //开始时间
           long startTime = System.currentTimeMillis();
           //准备消息的监听器 监听那些消息成功了 那些失败了
           //消息确认成功，回调函数
           ConfirmCallback ackCallback = (deliveryTag, multiple) -> {
               System.out.println("确认的消息：" + deliveryTag);
           };
           //消息确认失败，回调函数
           /**
            * deliveryTag      消息的标识
            * multiple         是否批量确认
            */
           ConfirmCallback nackCallback = (deliveryTag, multiple) -> {
               System.out.println("未确认的消息：" + deliveryTag);
           };
           //监听失败也监听成功
           channel.addConfirmListener(ackCallback, nackCallback); //异步通知
           //批量发送消息
           for (int i = 0; i < 1000; i++) {
               String message = "异步确认" + i;
               channel.basicPublish("", QUEUE_NAME, null, message.getBytes());
           }
           //结束时间
           long stopTime = System.currentTimeMillis();
           System.out.println("发布批量确认消息耗时：" + (stopTime - startTime) + "ms");
       }
   ```

5. ##### 如何处理异步未确认消息

   最好的解决的解决方案就是把未确认的消息放到一个基于内存的能被发布线程访问的队列，比如说用 ConcurrentLinkedQueue 这个队列在 confirm callbacks 与发布线程之间进行消息的传递

   ```java
   /异步确认消息
       public static void publishMessageAsync() throws Exception {
           Channel channel = RabbitMQUtils.getChannel();
           String QUEUE_NAME = UUID.randomUUID().toString();
           channel.queueDeclare(QUEUE_NAME, false, false, false, null);
           //开启发布确认
           channel.confirmSelect();
   
           //线程安全有序的一个哈希表 适用于高并发的情况下
           //1.轻松的将消息和序号进行关联
           //2.轻松批量删除条目  只要给到序号
           //3.支持高并发
           ConcurrentSkipListMap<Long, String> outstandingConfirms = new ConcurrentSkipListMap<>();
           //开始时间
           long startTime = System.currentTimeMillis();
           //准备消息的监听器 监听那些消息成功了 那些失败了
           //消息确认成功，回调函数
           ConfirmCallback ackCallback = (deliveryTag, multiple) -> {
               //2.删除已经确认的消息  剩下的就是未确认的消息
               //判断是否为批量
               if (multiple) {
                   ConcurrentNavigableMap<Long, String> confirmed = outstandingConfirms.headMap(deliveryTag);
                   confirmed.clear();
               } else {
                   outstandingConfirms.remove(deliveryTag);
               }
   
               System.out.println("确认的消息：" + deliveryTag);
           };
           ConfirmCallback nackCallback = (deliveryTag, multiple) -> {
               //3.打印未确认的消息
               String message = outstandingConfirms.get(deliveryTag);
               System.out.println("未确认的消息内容" + message);
               System.out.println("未确认的消息标识：" + deliveryTag);
           };
           //监听失败也监听成功
           channel.addConfirmListener(ackCallback, nackCallback); //异步通知
           //批量发送消息
           for (int i = 0; i < 1000; i++) {
               String message = "异步确认" + i;
               channel.basicPublish("", QUEUE_NAME, null, message.getBytes());
               //1.记录所有要发送的消息  消息的总和
               outstandingConfirms.put(channel.getNextPublishSeqNo(), message);
           }
           //结束时间
           long stopTime = System.currentTimeMillis();
           System.out.println("发布批量确认消息耗时：" + (stopTime - startTime) + "ms");
       }
   ```

6. ##### 以上三种发布确认速度对比

   **单个确认：**发布1000个消息到同一队列,耗时1863ms。

   **批量确认：**发布1000个批量消息到同一队列,耗时150ms。

   **异步确认：**发布1000个异步消息到同一队列,耗时99ms

### 7.交换机

​	 在上一节中，我们创建了一个工作队列。我们假设的是工作队列背后，每个任务都恰好交付给一个消费者(工作进程)。在这一部分中，我们将做一些完全不同的事情-我们将消息传达给多个消费者。这种模式称为“发布/订阅”.为了说明这种模式，我们将构建一个简单的日志系统。它将由两个程序组成:第一个程序将发出日志消息，第二个程序是消费者。其中我们会启动两个消费者，其中一个消费者接收到消息后把日志存储在磁盘另外一个消费者接收到消息后把消息打印在屏幕上，事实上第一个程序发出的日志消息将广播给所有消费者

#### 7.1 Exchanges

1. **Exchanges概念**

   ​	RabbitMO 消息传递模型的核心思想是: 生产者生产的消息从不会直接发送到队列。实际上，通常生产者甚至都不知道这些消息传递传递到了哪些队列中。
   ​	相反，生产者只能将消息发送到交换机(exchange)，交换机工作的内容非常简单，一方面它接收来自生产者的消息，另一方面将它们推入队列。交换机必须确切知道如何处理收到的消息。是应该把这些消息放到特定队列还是说把他们到许多队列中还是说应该丢弃它们。这就的由交换机的类型来决定。

2. **Exchanges的类型**

   1. 直接(direct)    路由类型
   2. 主题(topic)
   3. 标题(headers)   头类型(不常用)
   4. 扇出(fanot)     发布订阅类型

3. **无名Exchange**(默认类型)

   在本教程的前面部分我们对 exchange 一无所知，但仍然能够将消息发送到队列。之前能实现的原因是因为我们使用的是默认交换，我们通过空字符串("")进行标识。

   ```java
   channel.basicPublish("","hello",null,message.getBytes());
   ```

   第一个参数是交换机的名称。空字符串表示默认或无名称交换机: 消息能路由发送到队列中其实是由routingKey(bindingkey)绑定 key 指定的，如果它存在的话

#### 7.2 临时队列

​	每当我们连接到 Rabbt 时，我们都需要一个全新的空队列，为此我们可以创建一个具有随机名称的队列，或者能让服务器为我们选择一个随机队列名称那就更好了。其次一旦我们断开了消费者的连接，队列将被自动删除。

创建临时队列：channel.queueDeclare().getQueue()

#### 7.3 绑定(bindings)

binding就是exchange和queue之间的桥梁，告诉我们那个队列对应那个交换机

### 8. Fanout(发布订阅模式)

1. Fanout介绍

   Fanout 这种类型非常简单。正如从名称中猜到的那样，它是将接收到的所有消息广播到它知道的所有队列中。系统中默认有些 exchange 类型

2. Fanout实战

   ```java
   public class ReceiveLogs01 {
       private static final String EXCHANGES_NAME = "logs";
   
       public static void main(String[] args) throws Exception {
           Channel channel = RabbitMQUtils.getChannel();
           //声明一个交换机
           channel.exchangeDeclare(EXCHANGES_NAME, "fanout");
           //声明一个队列   临时队列
           String queue = channel.queueDeclare().getQueue();
           //绑定交换机和队列
           channel.queueBind(queue, EXCHANGES_NAME, "");
           System.out.println("等待接收消息...");
           //接收消息
           DeliverCallback deliverCallback = ((consumerTag, message) -> System.out.println("01接收到的消息：" + new String(message.getBody())));
           channel.basicConsume(queue, true, deliverCallback, consumerTag -> {
           });
       }
   }
   ```

   ```java
   public class ReceiveLogs02 {
       private static final String EXCHANGES_NAME = "logs";
   
       public static void main(String[] args) throws Exception {
           Channel channel = RabbitMQUtils.getChannel();
           //声明一个交换机
           channel.exchangeDeclare(EXCHANGES_NAME, "fanout");
           //声明一个队列   临时队列
           String queue = channel.queueDeclare().getQueue();
           //绑定交换机和队列
           channel.queueBind(queue, EXCHANGES_NAME, "");
           System.out.println("等待接收消息...");
           //接收消息
           DeliverCallback deliverCallback = ((consumerTag, message) -> System.out.println("02接收到的消息：" + new String(message.getBody())));
           channel.basicConsume(queue, true, deliverCallback, consumerTag -> {
           });
       }
   }
   
   ```

   ```java
   //发送消息   交换机
   public class EmitLog {
       private static final String EXCHANGES_NAME = "logs";
   
       public static void main(String[] args) throws Exception {
           Channel channel = RabbitMQUtils.getChannel();
           channel.exchangeDeclare(EXCHANGES_NAME, "fanout");
   
           Scanner scanner = new Scanner(System.in);
           while (scanner.hasNext()) {
               String message = scanner.next();
               channel.basicPublish(EXCHANGES_NAME, "", null, message.getBytes("UTF-8"));
               System.out.println("生产者发送消息：" + message);
           }
       }
   }
   ```

### 9. Direct Exchange(路由模式)

1. Direct Exchange

   队列只对它绑定的交换机的消息感兴趣。绑定用参数: routingKey 来表示也可称该参数为 binding key,创建绑定我们用代码:channel.queueBind(queueName, EXCHANGE_NAME,"routingKey");绑定之后的意义由其交换类型决定

2. 多重绑定

   当然果exchange 的绑定类型是 direct，但是它绑定的多个队列的 key 如果都相同，在这种情况下虽然绑定类型是 direct 但是它表现的就和fanout 有点类似了，就跟广播差不多

3. 实战

   ```java
   public class ReceiveLogsDirect01 {
       //交换机名字
       private static final String EXCHANGE_NAME = "direct_logs";
       public static void main(String[] args) throws Exception {
           Channel channel = RabbitMQUtils.getChannel();
           //声明交换机
           channel.exchangeDeclare(EXCHANGE_NAME, BuiltinExchangeType.DIRECT);
           channel.queueDeclare("console", false, false, false, null);
           channel.queueBind("console", EXCHANGE_NAME, "info");
           channel.queueBind("console", EXCHANGE_NAME, "warning");
           DeliverCallback deliverCallback = (consumerTag, message) -> System.out.println("一号队列接收到的消息：" + new String(message.getBody()));
           channel.basicConsume("console", deliverCallback, consumerTag -> {
           });
       }
   }
   ```

   ```java
   
   public class ReceiveLogsDirect02 {
       //交换机名字
       private static final String EXCHANGE_NAME = "direct_logs";
       public static void main(String[] args) throws Exception {
           Channel channel = RabbitMQUtils.getChannel();
           //声明交换机
           channel.exchangeDeclare(EXCHANGE_NAME, BuiltinExchangeType.DIRECT);
           channel.queueDeclare("disk", false, false, false, null);
           channel.queueBind("disk", EXCHANGE_NAME, "error");
           DeliverCallback deliverCallback = (consumerTag, message) -> System.out.println("二号队列接收到的消息：" + new String(message.getBody()));
           channel.basicConsume("disk", deliverCallback, consumerTag -> {
           });
       }
   }
   ```

   ```java
   public class DirectLogs {
       private static final String EXCHANGES_NAME = "direct_logs";
       public static void main(String[] args) throws Exception {
           Channel channel = RabbitMQUtils.getChannel();
           channel.exchangeDeclare(EXCHANGES_NAME, BuiltinExchangeType.DIRECT);
   
           Scanner scanner = new Scanner(System.in);
           while (scanner.hasNext()) {
               String message = scanner.next();
               channel.basicPublish(EXCHANGES_NAME, "error", null, message.getBytes("UTF-8"));
               System.out.println("生产者发送消息：" + message);
           }
       }
   }
   ```

### 10. Topics(主题模式)

1. 之前类型的问题

   尽管使用 direct 交换机改进了我们的系统，但是它仍然存在局限性-比方说我们想接收的日志类型有info.base 和 info.advantage，某个队列只想 info.base 的消息，那这个时候 direct 就办不到了。这个时候就只能使用 topic 类型

2. Topic的要求

   ​	发送到类型是 topic 交换机的消息的 routing_key 不能随意写，必须满足一定的要求，它**必须是一个单词列表，以点号分隔开**。这些单词可以是任意单词

   比如说:"stockusd.nyse"，"nyse.vmw”"quick.orange.rabbit".这种类型的。当然这个单词列表**最多不能超过 255 个字节**。

   > *(星号)可以代替一个单词
   >
   > #(井号)可以代替零个或多个单词
   >
   > 当一个队列绑定#，那么这个队列将接收所有数据，就像fanout
   >
   > 当一个队列没有出现*和#，那么该队列绑定类型就是direct

3. 实战

   ```java
   public class ReceiverLogsTopic01 {
       //交换机名称
       public static final String EXCHANGE_NAME = "topic_logs";
   
       public static void main(String[] args) throws Exception {
           Channel channel = RabbitMQUtils.getChannel();
           channel.exchangeDeclare(EXCHANGE_NAME, BuiltinExchangeType.TOPIC);
           String QUEUE_NAME = "Q1";
           channel.queueDeclare(QUEUE_NAME, false, false, false, null);
           channel.queueBind(QUEUE_NAME, EXCHANGE_NAME, "*.orange.*");
           System.out.println("等待接收消息。。。");
           //接收消息
           DeliverCallback deliverCallback = (consumerTag, message) -> System.out.println("C1接收到的消息：" + new String(message.getBody(), "UTF-8")+ " 绑定的key：" + message.getEnvelope().getRoutingKey());
           channel.basicConsume(QUEUE_NAME, true, deliverCallback, consumerTag -> System.out.println(consumerTag));
       }
   }
   ```

   ```java
   public class ReceiverLogsTopic02 {
       //交换机名称
       public static final String EXCHANGE_NAME = "topic_logs";
   
       public static void main(String[] args) throws Exception {
           Channel channel = RabbitMQUtils.getChannel();
           channel.exchangeDeclare(EXCHANGE_NAME, BuiltinExchangeType.TOPIC);
           String QUEUE_NAME = "Q2";
           channel.queueDeclare(QUEUE_NAME, false, false, false, null);
           channel.queueBind(QUEUE_NAME, EXCHANGE_NAME, "**.rabbit");
           channel.queueBind(QUEUE_NAME, EXCHANGE_NAME, "lazy.#");
           System.out.println("等待接收消息。。。");
           //接收消息
           DeliverCallback deliverCallback = (consumerTag, message) -> System.out.println("C2接收到的消息：" + new String(message.getBody(), "UTF-8")+ " 绑定的key：" + message.getEnvelope().getRoutingKey());
           channel.basicConsume(QUEUE_NAME, true, deliverCallback, consumerTag -> System.out.println(consumerTag));
       }
   }
   ```

   ```java
   public class EmitLogTopic {
       //交换机名称
       public static final String EXCHANGE_NAME = "topic_logs";
       public static void main(String[] args) throws Exception {
           Channel channel = RabbitMQUtils.getChannel();
           Map<String, String> map = new HashMap<>();
           map.put("quick.orange.rabbit", "被队列Q1Q2接收");
           map.put("lazy.orange.elephant", "被队列Q1Q2接收");
           map.put("quick.orange.fox", "被队列Q1接收");
           map.put("lazy.brown.fox", "被队列Q2接收");
           map.put("lazy.pink.rabbit", "虽然满足两个绑定但只被队列Q2接收一次");
           map.put("quick.brown.fox", "不匹配任何绑定，没有队列接收，会被丢弃");
           map.put("quick.orange.male.rabbit", "是四个单词不匹配任何绑定会被丢弃");
           map.put("lazy.orange.male.rabbit", "是四个单词但匹配Q2");
           for (Map.Entry<String, String> entry : map.entrySet()) {
               String routingKey = entry.getKey();
               String message = entry.getValue();
               channel.basicPublish(EXCHANGE_NAME, routingKey, null, message.getBytes("UTF-8"));
               System.out.println("生产者发送：" + message);
           }
       }
   }
   ```

### 11. 死信队列

**11.1 死信的概念**

先从概念解释上搞清楚这个定义，死信，顾名思义就是无法被消费的消息，字面意思可以这样理解，一般来说，producer 将消息投递到 broker 或者直接到queue 里了，consumer 从 queue 取出消息进行消费，但某些时候由于特定的原因导致 queue 中的某些消息无法被消费，这样的消息如果没有后续的处理，就变成了死信，有死信自然就有了死信队列。

**应用场景**：为了保证订单业务的消息数据不丢失，需要使用到 RabbitMQ 的死信队列机制，当消息消费发生异常时，将消息投入死信队列中。还有比如说：用户在商城下单成功并点击去支付后在指定时间未支付时自动失效。

**11.2 死信的来源**

- 消息 TTL（存活时间） 过期
- 队列达到最大长度(队列满了，无法再添加数据到队列中)
- 消息被拒绝（消费方拒绝应答：basic.reject 或 basic.nack）并且（不放回队列中： requeue=false）

**11.3 死信实战**

消息 TTL（存活时间） 过期

```java
//死信队列
//消费者1
public class Consumer01 {
    //普通交换机
    public static final String NORMAL_EXCHANGE = "normal_exchange";
    //死信交换机
    public static final String DEAD_EXCHANGE = "dead_exchange";
    //普通队列名
    public static final String NORMAL_QUEUE_NAME = "normal_queue";
    //死信队列名
    public static final String DEAD_QUEUE_NAME = "dead_queue";
    public static void main(String[] args) throws Exception {
        Channel channel = RabbitMQUtils.getChannel();
        //声明交换机
        channel.exchangeDeclare(NORMAL_EXCHANGE, BuiltinExchangeType.DIRECT);
        channel.exchangeDeclare(DEAD_EXCHANGE, BuiltinExchangeType.DIRECT);
        //声明队列
        //普通队列
        /**
         * 参数 ，满足死信三个条件转发到死信队列
         */
        Map<String, Object> arguments = new HashMap<>();
        //过期时间(单位：毫秒)   生产者发送的时候可以设置该消息时长
        //arguments.put("x-message-ttl", 10000);
        // 正常队列设置死信交换机
        arguments.put("x-dead-letter-exchange", DEAD_EXCHANGE);
        //设置死信RoutingKey
        arguments.put("x-dead-letter-routing-key", "lisi");
        channel.queueDeclare(NORMAL_QUEUE_NAME, false, false, false, arguments);
        //死信队列
        channel.queueDeclare(DEAD_QUEUE_NAME, false, false, false, null);
        //绑定交换机和队列
        channel.queueBind(NORMAL_QUEUE_NAME, NORMAL_EXCHANGE, "zhangsan");
        channel.queueBind(DEAD_QUEUE_NAME, DEAD_EXCHANGE, "lisi");
        System.out.println("等待接收消息....");

        DeliverCallback deliverCallback = (consumerTag, message) -> System.out.println("C1接收的消息" + new String(message.getBody(), "UTF-8"));
        channel.basicConsume(NORMAL_QUEUE_NAME, true, deliverCallback, consumerTag -> {
        });
    }
}
```

```java
//死信队列
//消费者2
public class Consumer02 {
    //死信队列名
    public static final String DEAD_QUEUE_NAME = "dead_queue";
    public static void main(String[] args) throws Exception {
        Channel channel = RabbitMQUtils.getChannel();
        System.out.println("等待接收消息....");

        DeliverCallback deliverCallback = (consumerTag, message) -> System.out.println("C2接收的消息" + new String(message.getBody(), "UTF-8"));
        channel.basicConsume(DEAD_QUEUE_NAME, true, deliverCallback, consumerTag -> {
        });
    }
}

```

```java
//死信队列
//生产者
public class Producer {
    //普通交换机
    public static final String NORMAL_EXCHANGE = "normal_exchange";
    public static void main(String[] args) throws Exception {
        Channel channel = RabbitMQUtils.getChannel();
        //死信消息  设置TTl
        AMQP.BasicProperties properties = new AMQP.BasicProperties().builder().expiration("10000").build();

        for (int i = 1; i < 11; i++) {
            String message = "info" + i;
            channel.basicPublish(NORMAL_EXCHANGE, "zhangsan", properties, message.getBytes());
        }
    }
}
```

队列达到最大长度

```java
//声明队列
//普通队列
/**
* 参数 ，满足死信三个条件转发到死信队列
*/
Map<String, Object> arguments = new HashMap<>();
//过期时间(单位：毫秒)   生产者发送的时候可以设置该消息时长
//arguments.put("x-message-ttl", 10000);
// 正常队列设置死信交换机
arguments.put("x-dead-letter-exchange", DEAD_EXCHANGE);
//设设置队列最大长度
arguments.put("x-max-length",6);
//设置死信RoutingKey
arguments.put("x-dead-letter-routing-key", "lisi");
channel.queueDeclare(NORMAL_QUEUE_NAME, false, false, false, arguments);
```

消息被拒绝

```java
 DeliverCallback deliverCallback = (consumerTag, message) -> {
 if("info5".equals(message)){
    System.out.println("C1拒绝此消息："+new String(message.getBody(), "UTF-8"));
    channel.basicReject(message.getEnvelope().getDeliveryTag(),false(是否放回队列))
 }else{
      System.out.println("C1接收的消息" + new String(message.getBody(), "UTF-8"));
     channel.basicAck(message.getEnvelope().getDeliveryTag(),false)
 }
 }
//开启手动应答
 channel.basicConsume(NORMAL_QUEUE_NAME, false, deliverCallback, consumerTag -> {
        });
```

### 12.延迟队列

##### 12.1 延迟队列概念

延迟队列存储的对象是对应的延迟消息，所谓“延迟消息”是指当消息被发送以后，并不想让消费者立刻拿到消息，而是等待特定时间后，消费者才能拿到这个消息进行消费。

##### 12.2 延迟队列使用场景

1. 订单在十分钟为支付则自动取消

2. 新创建的店铺，如果在十天之内都没有上传过商品，则自动发送消息提醒

3. 用户注册成功后，如果三天没有登录则进行短信提示

4. 用户发起退款，如果三天没有得到处理则通知相关运营人员

5. 预定会议后，需要在预定的时间点前十分钟通知各个与会人员参加会议

   这些场景都有一个特点，需要在某个事件发生之后或者之前的指定时间点完成某一项任务，如:发生订单生成事件，在十分钟之后检查该订单支付状态，然后将未支付的订单进行关闭;看起来似乎使用定时任务，一直轮询数据，每秒查一次，取出需要被处理的数据，然后处理不就完事了吗? 如果数据量比较少，确实可以这样做，比如: 对于“如果账单一周内未支付则进行自动结算”这样的需求如果对于时间不是严格限制，而是宽松意义上的一周，那么每天晚上跑个定时任务检查一下所有未支付的账单，确实也是一个可行的方案。但对于数据量比较大，并且时效性较强的场景，如:“订单十分钟内末支付则关闭“，短期内末支付的订单数据可能会有很多，活动期间甚至会达到百万甚至干万级别，对这么庞大的数据量仍旧使用轮询的方式显然是不可取的，很可能在一秒内无法完成所有订单的检查，同时会给数据库带来很大压力，无法满足业务要求而且性能低下。

##### 12.3 springboot整合

1. 创建项目

2. 导入依赖

   > <dependency>
   >     <groupId>org.springframework.boot</groupId>
   >     <artifactId>spring-boot-starter-amqp</artifactId>
   > </dependency>
   >
   > <dependency>
   >     <groupId>org.springframework.amqp</groupId>
   >     <artifactId>spring-rabbit-test</artifactId>
   >     <scope>test</scope>
   > </dependency>
   >
   > 其他的按自己需求导入

3. 配置队列TTL配置文件

   ```java
   @Configuration
   public class TtlQueueConfig {
   
       //声明交换机
       private static final String X_EXCHANGE = "x";
       private static final String Y_DEAD_EXCHANGE = "y";
       //声明队列
       private static final String QUEUE_A = "QA";
       private static final String QUEUE_B = "QB";
       private static final String QUEUE_DEAD_B = "QD";
   
       //声明普通交换机
       @Bean("xExchange")
       public DirectExchange xExchange() {
           return new DirectExchange(X_EXCHANGE);
       }
   
       //声明死信交换机
       @Bean("yExchange")
       public DirectExchange yExchange() {
           return new DirectExchange(Y_DEAD_EXCHANGE);
       }
   
       //声明普通队列    TTL=10s
       @Bean("queueA")
       public Queue queueA() {
           Map<String, Object> map = new HashMap<>(3);
           //设置死信交换机
           map.put("x-dead-letter-exchange", Y_DEAD_EXCHANGE);
           //设置死信key
           map.put("x-dead-letter-routing-key", "YD");
           //设置TTL
           map.put("x-message-ttl", 10000);
           return QueueBuilder.durable(QUEUE_A).withArguments(map).build();
       }
   
       //声明普通队列    TTL=10s
       @Bean("queueB")
       public Queue queueB() {
           Map<String, Object> map = new HashMap<>(3);
           //设置死信交换机
           map.put("x-dead-letter-exchange", Y_DEAD_EXCHANGE);
           //设置死信key
           map.put("x-dead-letter-routing-key", "YD");
           //设置TTL
           map.put("x-message-ttl", 40000);
           return QueueBuilder.durable(QUEUE_B).withArguments(map).build();
       }
   
       //死信队列
       @Bean()
       public Queue queueD() {
           return QueueBuilder.durable(QUEUE_DEAD_B).build();
       }
   
       //绑定
       @Bean
       public Binding queueABindingX(@Qualifier("queueA") Queue queueA,
                                     @Qualifier("xExchange") DirectExchange xExchange) {
           return BindingBuilder.bind(queueA).to(xExchange).with("XA");
       }
       @Bean
       public Binding queueBBindingX(@Qualifier("queueB") Queue queueB,
                                     @Qualifier("xExchange") DirectExchange xExchange) {
           return BindingBuilder.bind(queueB).to(xExchange).with("XB");
       }
       @Bean
       public Binding queueDBindingY(@Qualifier("queueD") Queue queueD,
                                     @Qualifier("yExchange") DirectExchange yExchange) {
           return BindingBuilder.bind(queueD).to(yExchange).with("YB");
       }
   }
   ```

4. 生产者代码

   ```java
   //发送延迟消息
   @RestController
   @RequestMapping("/ttl")
   @Slf4j
   public class SendMsgController {
   
       @Autowired
       private RabbitTemplate rabbitTemplate;
   
       //开始发送消息
       @GetMapping("/sendMsg/{message}")
       public void sendMsg(@PathVariable String message) {
           log.info("当前时间：{}，发送一条信息给两个TTL队列：{}", new Date().toString(), message);
           rabbitTemplate.convertAndSend("X", "XA", "消息来自ttl为10s的队列:" + message);
           rabbitTemplate.convertAndSend("X", "XB", "消息来自ttl为40s的队列:" + message);
       }
   }消费者
   ```
   
5. 消费者代码

   ```java
   @Slf4j
   @Component
   public class DeadLetterQueue {
       //接收消息
       @RabbitListener(queues = "QD")
       public void receiveD(Message message, Channel channel) {
           String msg = new String(message.getBody());
           log.info("当前时间：{}，收到死信队列的消息：{}", new Date().toString(), msg);
       }
   }
   ```
   
6. 代码优化(自定义消息时长)

   优化配置类

   ```java
   @Configuration
   public class TtlQueueConfig {
    private static final String QUEUE_C = "QC";
         @Bean("queueC")
       public Queue queueC() {
           Map<String, Object> map = new HashMap<>(3);
           //设置死信交换机
           map.put("x-dead-letter-exchange", Y_DEAD_EXCHANGE);
           //设置死信key
           map.put("x-dead-letter-routing-key", "XC");
           return QueueBuilder.durable(QUEUE_C).withArguments(map).build();
       }
   }
   ```

   优化生产者

   ```java
    @GetMapping("/sendExpirationMsg/{message}/{ttlTime}")
       public void sendMsg(@PathVariable String message, @PathVariable String ttlTime) {
           log.info("当前时间：{}，发送一条时长{}毫秒TTL信息给队列QC：{}", new Date().toString(), ttlTime, message);
           rabbitTemplate.convertAndSend("X", "XC", "" + message, msg -> {
               //发送消息的时候，设置时长
               msg.getMessageProperties().setExpiration(ttlTime);
               return msg;
           });
       }
   ```

   看起来似乎没什么问题，但是在最开始的时候，就介绍过如果使用在消息属性上设置 TTL 的方式，消息可能并不会按时“死亡“，因为 **RabbitMo 只会检查第一个消息是否过期**，如果过期则丢到死信队列**如果第一个消息的延时时长很长，而第二个消息的延时时长很短，第二个消息并不会优先得到执行**



































