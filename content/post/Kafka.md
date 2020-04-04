

### Kafka 入门

#### Kafka 是什么

Kafka 是分布式的流处理平台。

Kafka 主要的应用场景大体分为两类:

1. 在多个应用之间构建一条实时流式数据管道
2. 构建用于响应或修改流数据的应用

#### Kafka 中的几个概念

1. Topic  
	Kafka 中给流记录是按分类来存储的，每个分类就是一个 Topic
2. Broker
	消息中间件处理节点，一个 Kafka 节点就是一个 Broker，一个或者多个 Broker 可以组成一个 Kafka 集群，一般情况下 Kafka 都是已集群方式运行的，由 Zookeeper 来管理每个节点
2. Producer  
	数据生产者
	它的职责是选择分配记录给哪个 Topic 的哪个 Partition，一般采用的策略是轮训，也可以根据记录的 key 进行自定义配置

3. Consumer  
	数据消费者  
	每个消费者保留的唯一元数据是该消费者在日志中的 offset 或 position，消费者可以以任意顺序来处理记录
	![log_consumer](https://hurryking.github.io/img/log_consumer.png)

	消费者默认都会有一个消费者组，发布到一个 Topic 的记录会被发送到每个消费者组内的每个消费者，消费者组中的消费者可以使用在多个进程中或者多个机器中。

	![consumer_group](https://hurryking.github.io/img/consumer_groups.png)

4. Partition  
	物理上的概念，一个 Topic 可以分为多个 Partition，每个 Partition 内部是有序的

	![Partition](https://hurryking.github.io/img/Kafka_Topic.png)

	每条记录都会分配一个顺序增长的 id 数字被称为 offset，这个数字在每个 Partition 内是唯一的

	Partition 的作用有两个。首先，它们允许日志扩展到超出单个服务器所能容纳的大小。每个单独的分区都必须适合承载它的服务器，但是一个主题可能有很多分区，因此它可以处理任意数量的数据。 其次，它们当做并行单元来处理。

#### 给出一组 Kafka 集群运行案例

![consumer_groups](https://hurryking.github.io/img/consumer_groups.png)

案例中一个集群中有两个 Broker，每个 Broker 有两个 Partition，有两个消费者组，消费者组 A 有两个消费者，消费者组 B 有 4 个消费者。


#### 快速上手

理解了概念后我们就可以下载 Kafka 来操作一番
