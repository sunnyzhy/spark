# RabbitMQ介绍
RabbitMQ是实现AMQP（高级消息队列协议）的消息中间件的一种，最初起源于金融系统，用于在分布式系统中存储转发消息，在易用性、扩展性、高可用性等方面表现不俗。RabbitMQ主要是为了实现系统之间的双向解耦而实现的。当生产者大量产生数据时，消费者无法快速消费，那么需要一个中间层。保存这个数据。

AMQP，即Advanced Message Queuing Protocol，高级消息队列协议，是应用层协议的一个开放标准，为面向消息的中间件设计。消息中间件主要用于组件之间的解耦，消息的发送者无需知道消息使用者的存在，反之亦然。AMQP的主要特征是面向消息、队列、路由（包括点对点和发布/订阅）、可靠性、安全。

RabbitMQ是一个开源的AMQP实现，服务器端用Erlang语言编写，支持多种客户端，如：Python、Ruby、.NET、Java、JMS、C、PHP、ActionScript、XMPP、STOMP等，支持AJAX。用于在分布式系统中存储转发消息，在易用性、扩展性、高可用性等方面表现不俗。

# 相关概念
通常我们谈到队列服务, 会有三个概念： 发消息者、队列、收消息者，RabbitMQ 在这个基本概念之上, 多做了一层抽象, 在发消息者和 队列之间, 加入了交换器 (Exchange). 这样发消息者和队列就没有直接联系, 转而变成发消息者把消息给交换器, 交换器根据调度策略再把消息再给队列。

![](images/RabbitMQ01.png)

- 左侧 P 代表 生产者，也就是往 RabbitMQ 发消息的程序。

- 中间即是 RabbitMQ，其中包括了 交换机 和 队列。

- 右侧 C 代表 消费者，也就是往 RabbitMQ 拿消息的程序。

其中比较重要的概念有 4 个，分别为：虚拟主机，交换机，队列，和绑定。

- 虚拟主机：一个虚拟主机持有一组交换机、队列和绑定。为什么需要多个虚拟主机呢？很简单，RabbitMQ当中，用户只能在虚拟主机的粒度进行权限控制。 因此，如果需要禁止A组访问B组的交换机/队列/绑定，必须为A和B分别创建一个虚拟主机。每一个RabbitMQ服务器都有一个默认的虚拟主机“/”。

- 交换机：Exchange 用于转发消息，但是它不会做存储 ，如果没有 Queue bind 到 Exchange 的话，它会直接丢弃掉 Producer 发送过来的消息。
这里有一个比较重要的概念：路由键 。消息到交换机的时候，交互机会转发到对应的队列中，那么究竟转发到哪个队列，就要根据该路由键。

- 绑定：也就是交换机需要和队列相绑定，这其中如上图所示，是多对多的关系。

# 交换机(Exchange)
交换机的功能主要是接收消息并且转发到绑定的队列，交换机不存储消息，在启用ack模式后，交换机找不到队列会返回错误。交换机有四种类型：Direct, topic, Headers and Fanout

- Direct：direct 类型的行为是"先匹配, 再投送". 即在绑定时设定一个 routing_key, 消息的routing_key 匹配时, 才会被交换器投送到绑定的队列中去.

- Topic：按规则转发消息（最灵活）

- Headers：设置header attribute参数类型的交换机

- Fanout：转发消息到所有绑定队列

## Direct Exchange
Direct Exchange是RabbitMQ默认的交换机模式，也是最简单的模式，根据key全文匹配去寻找队列。

## Topic Exchange
Topic Exchange 转发消息主要是根据通配符。 在这种交换机下，队列和交换机的绑定会定义一种路由模式，那么，通配符就要在这种路由模式和路由键之间匹配后交换机才能转发消息。

在这种交换机模式下：
- 路由键必须是一串字符，用点号（.） 隔开，比如说 agreements.us，或者 agreements.eu.stockholm 等。

- 路由模式必须包含一个 星号（*），主要用于匹配路由键指定位置的一个单词，比如说，一个路由模式是这样子：agreements..b.*，那么就只能匹配路由键是这样子的：第一个单词是 agreements，第四个单词是 b。 井号（#）就表示相当于一个或者多个单词，例如一个匹配模式是agreements.eu.berlin.#，那么，以agreements.eu.berlin开头的路由键都是可以的。

topic 和 direct 类似, 只是匹配上支持了"模式", 在"点分"的 routing_key 形式中, 可以使用两个通配符:
- *表示一个词.

- #表示零个或多个词.

## Headers Exchange
headers 也是根据规则匹配, 相较于 direct 和 topic 固定地使用 routing_key , headers 则是一个自定义匹配规则的类型.
在队列与交换器绑定时, 会设定一组键值对规则, 消息中也包括一组键值对( headers 属性), 当这些键值对有一对, 或全部匹配时, 消息被投送到对应队列.

## Fanout Exchange
Fanout Exchange 消息广播的模式，不管路由键或者是路由模式，会把消息发给绑定给它的全部队列，如果配置了routing_key会被忽略。

# springboot集成RabbitMQ
## 基础用法
### 配置pom包，主要是添加spring-boot-starter-amqp的支持
```xml
<dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-amqp</artifactId>
</dependency>
```

### 配置文件
```
spring.application.name=rabbitmq-server

spring.rabbitmq.host=192.168.0.1
spring.rabbitmq.port=5672
spring.rabbitmq.username=admin
spring.rabbitmq.password=admin
```

### 队列配置
```java
@Configuration
public class RabbitConfig {

    @Bean
    public Queue Queue() {
        return new Queue("hello");
    }
    
}
```

### 发送者
```java
public class HelloSender {

    @Autowired
    private AmqpTemplate rabbitTemplate;

    public void send() {
        String context = "hello " + new Date();
        System.out.println("Sender : " + context);
        this.rabbitTemplate.convertAndSend("hello", context);
    }
    
}
```

### 接收者
```java
@Component
@RabbitListener(queues = "hello")
public class HelloReceiver {

    @RabbitHandler
    public void process(String hello) {
        System.out.println("Receiver  : " + hello);
    }
    
}
```

### 测试
```java
@RunWith(SpringRunner.class)
@SpringBootTest
public class RabbitMqHelloTest {

    @Autowired
    private HelloSender helloSender;

    @Test
    public void hello() throws Exception {
        helloSender.send();
    }
    
}
```
**注意，发送者和接收者的queue name必须一致，不然不能接收。**

## 一对多发送
### 发送者
```java
    public void send(int i) {
        System.out.println("Sender : " + i);
        this.rabbitTemplate.convertAndSend("hello", i);
    }
```

注册两个接收者：Receiver1和Receiver2
### 接收者
```java
@Component
@RabbitListener(queues = "hello")
public class HelloReceiver {

    @RabbitHandler
    public void process1(int i) {
        System.out.println("Receiver1  : ****** " + i);
    }
    
    @RabbitHandler
    public void process2(int i) {
        System.out.println("Receiver1  : ****** " + i);
    }
    
}
```

### 测试
```java
@Test
public void oneToMany() throws Exception {
    for (int i = 0; i < 100; i++){
        helloSender.send(i);
    }
}
```
结果如下：
```
Receiver 1: ****** 11
Receiver 2: ****** 12
Receiver 2: ****** 14
Receiver 1: ****** 13
Receiver 2: ****** 15
Receiver 1: ****** 16
Receiver 1: ****** 18
Receiver 2: ****** 17
Receiver 2: ****** 19
Receiver 1: ****** 20
```
**一个发送者，N个接受者，消息均匀地发送到N个接收者中。**

## 多对多发送

复制一份发送者，加入标记，在一百个循环中相互交替发送
```java
@Test
    public void manyToMany() throws Exception {
        for (int i = 0; i < 100; i++){
            helloSender.send(i);
            helloSender2.send(i);
        }
}
```
结果如下：
```
Receiver 1: ****** 20
Receiver 2: ****** 20
Receiver 1: ****** 21
Receiver 2: ****** 21
Receiver 1: ****** 22
Receiver 2: ****** 22
Receiver 1: ****** 23
Receiver 2: ****** 23
Receiver 1: ****** 24
Receiver 2: ****** 24
Receiver 1: ****** 25
Receiver 2: ****** 25
```

## 对象的支持
```java
//发送者
public void send(User user) {
    System.out.println("Sender object: " + user.toString());
    this.rabbitTemplate.convertAndSend("object", user);
}
```

```java
//接受者
@RabbitHandler
public void process(User user) {
    System.out.println("Receiver object : " + user);
}
```

结果如下：

Sender object: User{name='admin', pass='admin'}
Receiver object : User{name='admin', pass='admin'}

## Topic Exchange

topic 是RabbitMQ中最灵活的一种方式，可以根据routing_key自由的绑定不同的队列

### 配置
```java
@Configuration
public class TopicRabbitConfig {

    final static String message = "topic.message";
    final static String messages = "topic.messages";

    @Bean
    public Queue queueMessage() {
        return new Queue(TopicRabbitConfig.message);
    }

    @Bean
    public Queue queueMessages() {
        return new Queue(TopicRabbitConfig.messages);
    }

    @Bean
    TopicExchange exchange() {
        return new TopicExchange("exchange");
    }

    @Bean
    Binding bindingExchangeMessage(Queue queueMessage, TopicExchange exchange) {
        return BindingBuilder.bind(queueMessage).to(exchange).with("topic.message");
    }

    @Bean
    Binding bindingExchangeMessages(Queue queueMessages, TopicExchange exchange) {
        return BindingBuilder.bind(queueMessages).to(exchange).with("topic.#");
    }
}
```

### 发送者
使用queueMessages同时匹配两个队列，queueMessage只匹配"topic.message"队列
```java
public void send1() {
    String context = "hi, i am message 1";
    System.out.println("Sender : " + context);
    this.rabbitTemplate.convertAndSend("exchange", "topic.message", context);
}

public void send2() {
    String context = "hi, i am messages 2";
    System.out.println("Sender : " + context);
    this.rabbitTemplate.convertAndSend("exchange", "topic.messages", context);
}
```java

### 接收者
```java
@Component
public class HelloReceiver2 {

    @RabbitListener(queues = "topic.message")
    @RabbitHandler
    public void processA(String str1) {
        System.out.println("topic.message:" + str1);
    }

    @RabbitListener(queues = "topic.messages")
    @RabbitHandler
    public void processB(String str) {
        System.out.println("topic.messages:" + str);
    }
    
}
```
发送send1会匹配到topic.#和topic.message 两个Receiver都可以收到消息，发送send2只有topic.#可以匹配所有只有Receiver2监听到消息

## Fanout Exchange
Fanout 就是我们熟悉的广播模式或者订阅模式，给Fanout交换机发送消息，绑定了这个交换机的所有队列都收到这个消息。

### 配置
```java
@Configuration
public class FanoutRabbitConfig {

    @Bean
    public Queue AMessage() {
        return new Queue("fanout.A");
    }

    @Bean
    public Queue BMessage() {
        return new Queue("fanout.B");
    }

    @Bean
    public Queue CMessage() {
        return new Queue("fanout.C");
    }

    @Bean
    FanoutExchange fanoutExchange() {
        return new FanoutExchange("fanoutExchange");
    }

    @Bean
    Binding bindingExchangeA(Queue AMessage,FanoutExchange fanoutExchange) {
        return BindingBuilder.bind(AMessage).to(fanoutExchange);
    }

    @Bean
    Binding bindingExchangeB(Queue BMessage, FanoutExchange fanoutExchange) {
        return BindingBuilder.bind(BMessage).to(fanoutExchange);
    }

    @Bean
    Binding bindingExchangeC(Queue CMessage, FanoutExchange fanoutExchange) {
        return BindingBuilder.bind(CMessage).to(fanoutExchange);
    }

}
```

### 发送者
这里使用了A、B、C三个队列绑定到Fanout交换机上面，发送端的routing_key写任何字符都会被忽略：
```java
public void send() {
        String context = "hi, fanout msg ";
        System.out.println("Sender : " + context);
        this.rabbitTemplate.convertAndSend("fanoutExchange","", context);
}
```

### 接收者
```java
@Component
public class HelloReceiver {

    @RabbitListener(queues = "fanout.A1")
    @RabbitHandler
    public void processA(String str1) {
        System.out.println("ReceiveA:" + str1);
    }

    @RabbitListener(queues = "fanout.B1")
    @RabbitHandler
    public void processB(String str) {
        System.out.println("ReceiveB:" + str);
    }

    @RabbitListener(queues = "fanout.C1")
    @RabbitHandler
    public void processC(String str) {
        System.out.println("ReceiveC:" + str);
    }

}
```

结果如下：
```
Sender : hi, fanout msg

fanout Receiver A: hi, fanout msg
fanout Receiver B: hi, fanout msg
fanout Receiver C: hi, fanout msg
```

### 从RabbitMQ的管理后台取消绑定
在Queues里选择fanout.C1队列，然后在Bindings里Unbind交换机，再发送消息。
结果如下：
```
Sender : hi, fanout msg

fanout Receiver A: hi, fanout msg
fanout Receiver B: hi, fanout msg
```
