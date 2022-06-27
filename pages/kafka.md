- Kafka 是什么？主要应用场景有哪些？
  Kafka 是一个分布式流式处理平台。这到底是什么意思呢？
  流平台具有三个关键功能：
  1. **消息队列**：发布和订阅消息流，这个功能类似于消息队列，这也是 Kafka 也被归类为消息队列的原因。
  2. **容错的持久方式存储记录消息流**： Kafka 会把消息持久化到磁盘，有效避免了消息丢失的风险。
  3. **流式处理平台：**在消息发布的时候进行处理，Kafka 提供了一个完整的流式处理类库。
  
  **Kafka 采用的是发布 - 订阅模型。**
  **RocketMQ 的消息模型和 Kafka 基本是完全一样的。唯一的区别是 Kafka 中没有队列这个概念，与之对应的是 Partition（分区）。**
- 主要应用场景
  Kafka 主要有两大应用场景：
  1. **消息队列**：建立实时流数据管道，以可靠地在系统或应用程序之间获取数据。
  2. **数据处理：**构建实时的流数据处理程序来转换或处理数据流。
- 和其他消息队列相比,Kafka的优势在哪里？
  我们现在经常提到 Kafka 的时候就已经默认它是一个非常优秀的消息队列了，我们也会经常拿它跟 RocketMQ、RabbitMQ 对比。我觉得 Kafka 相比其他消息队列主要的优势如下：
  1. **极致的性能**：基于 Scala 和 Java 语言开发，设计中大量使用了批量处理和异步的思想，最高可以每秒处理千万级别的消息。
  2. **生态系统兼容性无可匹敌**：Kafka 与周边生态系统的兼容性是最好的没有之一，尤其在大数据和流计算领域。
- kafka架构
  Kafka 比较重要的几个概念：
  1. **Producer（生产者）**: 产生消息的一方。
  2. **Consumer（消费者）**: 消费消息的一方。
  3.  **Broker（代理）**: 可以看作是一个独立的 Kafka 实例。多个 Kafka Broker 组成一个 Kafka Cluster。
  你一定也注意到每个 Broker 中又包含了 Topic 以及 Partition 这两个重要的概念：
  4. **Topic（主题）**: Producer 将消息发送到特定的主题，Consumer 通过订阅特定的 Topic(主题) 来消费消息。
  5. **Partition（分区）**: Partition 属于 Topic 的一部分。一个 Topic 可以有多个 Partition ，并且同一 Topic 下的 Partition 可以分布在不同的 Broker 上，这也就表明一个 Topic 可以横跨多个 Broker 。这正如我上面所画的图一样。
  Kafka 将生产者发布的消息发送到**Topic（主题）**中，需要这些消息的消费者可以订阅这些**Topic（主题）**，如下图所示
  ![image.png](../assets/image_1656332366658_0.png)
  --->划重点：**Kafka 中的 Partition（分区） 实际上可以对应成为消息队列中的队列。这样是不是更好理解一点？**
- Kafka 的多副本机制(高可用)
  Kafka 为分区（Partition）引入了多副本（Replica）机制。分区（Partition）中的多个副本之间会有一个叫做 leader 的家伙，其他副本称为 follower。我们发送的消息会被发送到 leader 副本，然后 follower 副本才能从 leader 副本中拉取消息进行同步。
  
  >生产者和消费者只与 leader 副本交互。你可以理解为其他副本只是 leader 副本的拷贝，它们的存在只是为了保证消息存储的安全性。当 leader 副本发生故障时会从 follower 中选举出一个 leader,但是 follower 中如果有和 leader 同步程度达不到要求的参加不了 leader 的竞选。
  
  **Kafka 的多分区（Partition）以及多副本（Replica）机制有什么好处呢？**
  1. Kafka 通过给特定 Topic 指定多个 Partition, 而各个 Partition 可以分布在不同的 Broker 上, 这样便能提供比较好的并发能力（负载均衡）。
  2. Partition 可以指定对应的 Replica 数, 这也极大地提高了消息存储的安全性, 提高了容灾能力，不过也相应的增加了所需要的存储空间。
- Zookeeper 在 Kafka 中的作用
  >**要想搞懂 zookeeper 在 Kafka 中的作用 一定要自己搭建一个 Kafka 环境然后自己进 zookeeper 去看一下有哪些文件夹和 Kafka 有关，每个节点又保存了什么信息。**一定不要光看不实践，这样学来的也终会忘记！[这部分内容参考和借鉴了这篇文章](https://www.jianshu.com/p/a036405f989c) 。
- 配置:无消息丢失配置怎么实现