# Kafka体系架构
![](images/kafka-1.png)

一个典型的Kafka体系架构包括若干Producer（可以是服务器日志，业务数据，页面前端产生的page view等等），若干broker（Kafka支持水平扩展，一般broker数量越多，集群吞吐率越高），若干Consumer (Group)，以及一个Zookeeper集群。Kafka通过Zookeeper管理集群配置，选举leader，以及在consumer group发生变化时进行rebalance。Producer使用push(推)模式将消息发布到broker，Consumer使用pull(拉)模式从broker订阅并消费消息。

名称|解释
---|---
Broker|消息中间件处理节点，一个Kafka节点就是一个broker，一个或者多个Broker可以组成一个Kafka集群
Topic|Kafka根据topic对消息进行归类，发布到Kafka集群的每条消息都需要指定一个topic
Producer|消息生产者，向Broker发送消息的客户端
Consumer|消息消费者，从Broker读取消息的客户端
ConsumerGroup|每个Consumer属于一个特定的Consumer Group，一条消息可以发送到多个不同的Consumer Group，但是一个Consumer Group中只能有一个Consumer能够消费该消息
Partition|物理上的概念，一个topic可以分为多个partition，每个partition内部是有序的
