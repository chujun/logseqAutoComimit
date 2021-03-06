- `RocketMQ` 是一个**队列模型**的消息中间件，具有**高性能、高可靠、高实时、分布式**的特点。
  它是一个采用 `Java` 语言开发的分布式的消息系统，由阿里巴巴团队开发，在2016年底贡献给 `Apache` ，成为了 `Apache` 的一个顶级项目。 
  在阿里内部， `RocketMQ` 很好地服务了集团大大小小上千个应用，在每年的双十一当天，更有不可思议的万亿级消息通过 `RocketMQ` 流转。
- RocketMQ的消息模型
  `RocketMQ` 中的消息模型就是按照**主题模型**所实现的。 ((62b99489-5b99-41ed-9798-bcdb3a1587e6))
  ![image.png](../assets/image_1656405894302_0.png)
  整个图中有 `Producer Group` 、 `Topic` 、 `Consumer Group` 三个角色`
  1. Producer Group` 生产者组： 代表某一类的生产者，比如我们有多个秒杀系统作为生产者，这多个合在一起就是一个 `Producer Group` 生产者组，它们一般生产相同的消息。`
  2. Consumer Group` 消费者组： 代表某一类的消费者，比如我们有多个短信系统作为消费者，这多个合在一起就是一个 `Consumer Group` 消费者组，它们一般消费相同的消息。`
  3. Topic` 主题： 代表一类消息，比如订单消息，物流消息等等。
  
  生产者组中的生产者会向主题发送消息，而**主题中存在多个队列**，生产者每次生产消息之后是指定主题中的某个队列发送消息的。
  
  每个主题中都有多个队列(分布在不同的 `Broker` 中，如果是集群的话， `Broker` 又分布在不同的服务器中)，集群消费模式下，一个消费者集群多台机器共同消费一个 `topic` 的多个队列，**一个队列只会被一个消费者消费**。如果某个消费者挂掉，分组内其它消费者会接替挂掉的消费者继续消费。就像上图中 `Consumer1` 和 `Consumer2` 分别对应着两个队列，而 `Consumer3` 是没有队列对应的，所以一般来讲要控制**消费者组中的消费者个数和主题中队列个数相同**。
  当然也可以消费者个数小于队列个数，只不过不太建议。如下图。
  ![image.png](../assets/image_1656406210398_0.png)
	- **每个消费组在每个队列上维护一个消费位置**，为什么呢？
	  因为我们刚刚画的仅仅是一个消费者组，我们知道在发布订阅模式中一般会涉及到多个消费者组，而每个消费者组在每个队列中的消费位置都是不同的。如果此时有多个消费者组，那么消息被一个消费者组消费完之后是不会删除的(因为其它消费者组也需要呀)，它仅仅是为每个消费者组维护一个**消费位移(offset)**，每次消费者组消费完会返回一个成功的响应，然后队列再把维护的消费位移加一，这样就不会出现刚刚消费过的消息再一次被消费了。
	  ![image.png](../assets/image_1656406369556_0.png)
	- **为什么一个主题中需要维护多个队列**？
	  答案是**提高并发能力**。的确，每个主题中只存在一个队列也是可行的。你想一下，如果每个主题中只存在一个队列，这个队列中也维护着每个消费者组的消费位置，这样也可以做到**发布订阅模式**。如下图。
	  ![image.png](../assets/image_1656406477331_0.png) 
	  但是，这样我生产者是不是只能向一个队列发送消息？又因为需要维护消费位置所以一个队列只能对应一个消费者组中的消费者，这样是不是其他的 `Consumer` 就没有用武之地了？从这两个角度来讲，并发度一下子就小了很多。
	- 所以总结来说， `RocketMQ` 通过**使用在一个 `Topic` 中配置多个队列并且每个队列维护每个消费者组的消费位置**实现了**主题模式/发布订阅模式**。
- 消费者组消费特点
  id:: 62bac06c-eaa4-4c39-97ea-8119cb35bb69
  `RocketMQ` 通过**使用在一个 `Topic` 中配置多个队列并且每个队列维护每个消费者组的消费位置**实现了**主题模式/发布订阅模式**。
  
  1. 集群消费模式下，一个消费者集群多台机器共同消费一个 `topic` 的多个队列，**一个队列只会被一个消费者消费**。
  2. 如果某个消费者挂掉，分组内其它消费者会接替挂掉的消费者继续消费
  3. **每个消费组在topic每个队列上维护一个消费位置**
- RocketMQ的架构图
  id:: 62bac242-973f-4555-8de9-69347418ebc4
  `RocketMQ` 技术架构中有四大角色 `NameServer` 、 `Broker` 、 `Producer` 、 `Consumer`.
	- 1. `Broker` ： 主要负责消息的存储、投递和查询以及服务高可用保证。说白了就是消息队列服务器嘛，生产者生产消息到 `Broker` ，消费者从 `Broker` 拉取消息并消费.
	  这里，我还得普及一下关于 `Broker` 、 `Topic` 和 队列的关系。上面我讲解了 `Topic` 和队列的关系——一个 `Topic` 中存在多个队列，那么这个 `Topic` 和队列存放在哪呢？**
	  一个 `Topic` 分布在多个 `Broker` 上，一个 `Broker` 可以配置多个 `Topic` ，它们是多对多的关系**。
	  如果某个 `Topic` 消息量很大，应该给它多配置几个队列(上文中提到了提高并发能力)，并且**尽量多分布在不同 `Broker` 上，以减轻某个 `Broker` 的压力**。`
	  Topic` 消息量都比较均匀的情况下，如果某个 `broker` 上的队列越多，则该 `broker` 压力越大。
	  ![image.png](../assets/image_1656406670928_0.png)
	- 2. `NameServer` ：它其实也是一个**注册中心**，主要提供两个功能：**Broker管理**和**路由信息管理**。说白了就是 `Broker` 会将自己的信息注册到 `NameServer` 中，此时 `NameServer` 就存放了很多 `Broker` 的信息(Broker的路由表)，消费者和生产者就从 `NameServer` 中获取路由表然后照着路由表的信息和对应的 `Broker` 进行通信(生产者和消费者定期会向 `NameServer` 去查询相关的 `Broker` 的信息)。
	  `NameServer` 也做了集群部署，但是请注意它是**去中心化**的。节点之间互相不通信,节点状态完全一致
	  --->类似`ZooKeeper` 和 `Spring Cloud` 中的 `Eureka`
	- 3. `Producer` ： 消息发布的角色，支持分布式集群方式部署。说白了就是生产者。
	- 4. `Consumer` ： 消息消费的角色，支持分布式集群方式部署。支持以push推，pull拉两种模式对消息进行消费(不过RocketMQ的push模式本质上还是pull模式)。同时也支持集群方式和广播方式的消费，它提供实时消息订阅机制。说白了就是消费者。
	  听完了上面的解释你可能会觉得，这玩意好简单。不就是这样的么？
	  ![image.png](../assets/image_1656408378526_0.png)
	- 问题：RocketMQ为什么要引入NameServer角色
	  直接 `Producer` 、 `Consumer` 和 `Broker` 直接进行生产消息，消费消息不就好了么？
	  我们上文提到过 `Broker` 是需要保证高可用的，如果整个系统仅仅靠着一个 `Broker` 来维持的话，那么这个 `Broker` 的压力会不会很大？所以我们需要使用多个 `Broker`集群 来保证**负载均衡**。
	  如果说，我们的消费者和生产者直接和多个 `Broker` 相连，那么当 `Broker` 修改的时候必定会牵连着每个生产者和消费者，这样就会产生耦合问题，而 `NameServer` 注册中心就是用来解决这个问题的。
	- 问题: RocketMQ 不使用 ZooKeeper 作为注册中心的原因，以及自制的 NameServer 优缺点？
	  1. ZooKeeper(CAP中的CP) 作为支持顺序一致性的中间件，在某些情况下，它为了满足一致性，会丢失一定时间内的可用性，RocketMQ 需要注册中心只是为了发现组件地址，在某些情况下，RocketMQ 的注册中心可以出现数据不一致性，这同时也是 NameServer 的缺点，因为 NameServer 集群间互不通信，它们之间的注册信息可能会不一致(CAP中的AP)
	  2. 另外，当有新的服务器加入时，NameServer 并不会立马通知到 Producer，而是由 Producer 定时去请求 NameServer 获取最新的 Broker/Consumer 信息（这种情况是通过 Producer 发送消息时，负载均衡解决）
- RocketMQ集群模式
  当然， `RocketMQ` 中的技术架构肯定不止前面那么简单，因为上面图中的四个角色都是需要做集群的。 ((62bac242-973f-4555-8de9-69347418ebc4)) 
  给出一张官网的架构图，大家尝试理解一下。
  ![image.png](../assets/image_1656408536510_0.png)
  其实和我们最开始画的那张乞丐版的架构图也没什么区别，主要是一些细节上的差别。听我细细道来🤨。
  1. 第一、broker集群：我们的 `Broker` **做了集群并且还进行了主从部署**，由于消息分布在各个 `Broker` 上，一旦某个 `Broker` 宕机，则该 `Broker` 上的消息读写都会受到影响。所以 `Rocketmq` 提供了 `master/slave` 的结构， `[:span {} " "]` 定时从 `master` 同步数据(同步刷盘或者异步刷盘)，如果 `master` 宕机，**则 `slave` 提供消费服务，但是不能写入消息**(后面我还会提到哦)。
  2. 第二、NameServer集群:为了保证 `HA` ，我们的 `NameServer` 也做了集群部署，但是请注意它是**去中心化**的。也就意味着它没有主节点，你可以很明显地看出 `NameServer` 的所有节点是没有进行 `Info Replicate` 的，在 `RocketMQ` 中是通过**单个Broker和所有NameServer保持长连接**，并且在每隔30秒 `Broker` 会向所有 `Nameserver` 发送心跳，心跳包含了自身的 `Topic` 配置信息，这个步骤就对应这上面的 `Routing Info` 。
  --->每个节点存储状态完全一致,NameServer集群保证CAP中的AP
  3. 第三、发消息集群:在生产者需要向 `Broker` 发送消息的时候，**需要先从 `NameServer` 获取关于 `Broker` 的路由信息**，然后通过**轮询**的方法去向每个队列中生产数据以达到**负载均衡**的效果。
  4. 第四、消费消息集群:消费者通过 `NameServer` 获取所有 `Broker` 的路由信息后，向 `Broker` 发送 `Pull` 请求来获取消息数据。 
  `Consumer` 可以以两种模式启动——**广播（Broadcast）和集群（Cluster）**。
  广播模式下，一条消息会发送给**同一个消费组中的所有消费者**，
  集群模式下消息只会发送给一个消费者。
  
  在单主从架构中，如果一个主节点挂掉了，那么也就意味着整个系统不能再生产了。那么这个可用性的问题能否解决呢？**一个主从不行那就多个主从的呗**，别忘了在我们最初的架构图中，每个 `Topic` 是分布在不同 `Broker` 中的。
  ![image.png](../assets/image_1656423700154_0.png)
- 如何解决顺序消费
  在上面的技术架构介绍中，我们已经知道了** `RocketMQ` 在主题上是无序的、它只有在队列层面才是保证有序**的。
  这又扯到两个概念——**普通顺序**和**严格顺序**。
  1. 普通顺序是指 消费者通过**同一个消费队列收到的消息是有顺序的**，不同消息队列收到的消息则可能是无顺序的。普通顺序消息在 `Broker` **重启情况下不会保证消息顺序性**(短暂时间) 。
  2. 严格顺序是指 消费者收到的**所有消息**均是有顺序的。严格顺序消息**即使在异常情况下也会保证消息的顺序性**。
  但是，严格顺序看起来虽好，实现它可会付出巨大的代价。如果你使用严格顺序模式， `Broker` 集群中只要有一台机器不可用，则整个集群都不可用。你还用啥？现在主要场景也就在 `binlog` 同步。--->binlog需要保证严格的顺序性
  一般而言，我们的 `MQ` 都是能容忍短暂的乱序，所以推荐使用普通顺序模式。
  
  那么，我们现在使用了**普通顺序模式**，我们从上面学习知道了在 `Producer` 生产消息的时候会进行轮询(取决你的负载均衡策略)来向同一主题的不同消息队列发送消息。那么如果此时我有几个消息分别是同一个订单的创建、支付、发货，在轮询的策略下这**三个消息会被发送到不同队列**，因为在不同的队列此时就无法使用 `RocketMQ` 带来的队列有序特性来保证消息有序性了。
  ![image.png](../assets/image_1656409549616_0.png)
  
  那么，怎么解决呢？
  其实很简单，我们需要处理的仅仅是将同一语义下的消息放入同一个队列(比如这里是同一个订单)，那我们就可以使用**Hash取模法**来保证同一个订单在同一个队列中就行了。
- 如何解决重复消费
  解决方案:emmm，就两个字——**幂等**
  在编程中一个幂等操作的特点是其任意多次执行所产生的影响均与一次执行的影响相同。
  
  那么如何给业务实现幂等呢？这个还是需要结合具体的业务的。你可以使用**写入 `Redis` **来保证，因为 `Redis` 的 `key` 和 `value` 就是天然支持幂等的。当然还有使用**数据库插入法**，基于数据库的唯一键来保证重复数据不会被插入多条。
  
  不过最主要的还是需要**根据特定场景使用特定的解决方案**，你要知道你的消息消费是否是完全不可重复消费还是可以忍受重复消费的，然后再选择强校验和弱校验的方式。毕竟在 CS 领域还是很少有技术银弹的说法。
  
  而在整个互联网领域，幂等不仅仅适用于消息队列的重复消费问题，这些实现幂等的方法，也同样适用于，**在其他场景中来解决重复请求或者重复调用的问题**。比如将HTTP服务设计成幂等的，**解决前端或者APP重复提交表单数据的问题**，也可以将一个微服务设计成幂等的，解决 `RPC` 框架自动重试导致的**重复调用问题**。
- 如何解决消息丢失
- 消息堆积问题
  在上面我们提到了消息队列一个很重要的功能——**削峰**。那么如果这个峰值太大了导致消息堆积在队列中怎么办呢？
  原因:产生消息堆积的根源其实就只有两个——生产者生产太快或者消费者消费太慢。
  解决
  生产者角度
  1. 当流量到峰值的时候是因为生产者生产太快，我们可以使用一些**限流降级**的方法
  2. 
  消费者角度
  1. 如果消费者消费过慢的话，我们可以先检查**是否是消费者出现了大量的消费错误**，或者打印一下日志查看是否是哪一个线程卡死，出现了锁资源不释放等等的问题。
  2. 你也可以增加多个消费者实例去水平扩展增加消费能力来匹配生产的激增。
  >当然，最快速解决消息堆积问题的方法还是增加消费者实例，不过**同时你还需要增加每个主题的队列数量**。
  别忘了在 `RocketMQ` 中，**一个队列只会被一个消费者消费**，如果你仅仅是增加消费者实例就会出现我一开始给你画架构图的那种情况。
  
  ![image.png](../assets/image_1656422944990_0.png)
- RocketMQ推拉模式
  RocketMQ支持推push，拉pull两种消息消费模式，不过push模式本质上还是基于pull模式实现的。
  push模式实现
  1. 注意，RocketMQ 已经说了自己会有低延迟问题，其中就包括这个消息的 push 延迟问题
  2. 因为这并不是真正的将消息主动的推送到消费者，而是 Broker 定时任务每5s将消息推送到消费者
  3. pull模式需要我们手动调用consumer拉消息，而push模式则只需要我们提供一个listener即可实现对消息的监听，而实际上，RocketMQ的push模式是基于pull模式实现的，它没有实现真正的push。
  4. push方式里，consumer把向broker的轮询过程封装了，并注册MessageListener监听器，取到消息后，唤醒MessageListener的consumeMessage()来消费，对用户而言，感觉消息是被推送过来的。
- 消息回溯
  回溯消费是指 `Consumer` 已经消费成功的消息，由于业务上需求需要重新消费，在 `RocketMQ` 中， `Broker` 在向 `Consumer` 投递成功消息后，**消息仍然需要保留**。并且重新消费一般是按照时间维度，例如由于 `Consumer` 系统故障，恢复后需要重新消费1小时前的数据，那么 `Broker` 要提供一种机制，可以按照时间维度来回退消费进度。 `RocketMQ` 支持按照时间回溯消费，时间维度精确到毫秒。
- 分布式事务
  原理基于2PC，即两阶段提交，prepared->commit/rollback
  在 `RocketMQ` 中使用的是**事务消息加上事务反查机制**来解决分布式事务问题的。
  ![截屏2022-06-28 下午8.17.58.png](../assets/截屏2022-06-28_下午8.17.58_1656418702175_0.png) 
  在第一步发送的 half 消息 ，它的意思是**在事务提交之前，对于消费者来说，这个消息是不可见的**。
  >那么，如何做到写入消息但是对用户不可见呢？RocketMQ事务消息的做法是：如果消息是half消息，将备份原消息的主题与消息消费队列，然后**改变主题**为RMQ_SYS_TRANS_HALF_TOPIC。由于消费组未订阅该主题，故消费端无法消费half类型的消息，**然后RocketMQ会开启一个定时任务，从Topic为RMQ_SYS_TRANS_HALF_TOPIC中拉取消息进行消费**，根据生产者组获取一个服务提供者发送回查事务状态请求，根据事务状态来决定是提交或回滚消息。
  
  
  你还需要注意的是，在 `MQ Server` 指向系统B的操作已经和系统A不相关了，也就是说在消息队列中的分布式事务是——**本地事务和存储消息到消息队列才是同一个事务**。这样也就产生了事务的**最终一致性**，因为整个过程是异步的，**每个系统只要保证它自己那一部分的事务就行了**。
	- 事务消息发送步骤如下：
	  1. 生产者将半事务消息发送至消息队列RocketMQ版服务端。
	  2. 消息队列RocketMQ版服务端将消息持久化成功之后，向生产者返回Ack确认消息已经发送成功，此时消息为半事务消息。
	  3. 生产者开始执行本地事务逻辑。
	  4. 生产者根据本地事务执行结果向服务端提交二次确认结果（Commit或是Rollback），服务端收到确认结果后处理逻辑如下：
	  a. 二次确认结果为Commit：服务端将半事务消息标记为可投递，并投递给消费者。
	  b. 二次确认结果为Rollback：服务端将回滚事务，不会将半事务消息投递给消费者。
	  5. 在断网或者是生产者应用重启的特殊情况下，若服务端未收到发送者提交的二次确认结果，或服务端收到的二次确认结果为Unknown未知状态，经过固定时间后，服务端将对消息生产者即生产者集群中任一生产者实例发起消息回查。
	- 事务消息回查步骤如下：
	  1. 生产者收到消息回查后，需要检查对应消息的本地事务执行的最终结果。
	  2. 生产者根据检查得到的本地事务的最终状态再次提交二次确认，服务端仍按照步骤4对半事务消息进行处理。
	- 发送事务消息为什么必须要实现事务回查Check机制？
	  当步骤1中半事务消息发送完成，但本地事务返回状态为 `TransactionStatus.Unknow` ，或者应用退出导致本地事务未提交任何状态时，从Broker的角度看，这条半事务消息的状态是未知的。因此Broker会定期向消息发送方即消息生产者集群中的任意一生产者实例发起消息回查，要求发送方回查该Half状态消息，并上报其最终状态。
	- 使用规则
		- 生产消息规则
		  1.事务消息发送完成本地事务后，可在 `execute` 方法中返回以下三种状态：`
		  a.TransactionStatus.CommitTransaction` ：提交事务，允许消费者消费该消息。`
		  b.TransactionStatus.RollbackTransaction` ：回滚事务，消息将被丢弃不允许消费。`
		  c.TransactionStatus.Unknow` ：暂时无法判断状态，等待固定时间以后消息队列RocketMQ版服务端根据回查规则向生产者进行消息回查。
		  2.通过 `ONSFactory.createTransactionProducer` 创建事务消息的Producer时必须指定 `LocalTransactionChecker` 的实现类，处理异常情况下事务消息的回查。
		  3.回查规则：本地事务执行完成后，若消息队列RocketMQ版服务端收到的本地事务返回状态为 `TransactionStatus.Unknow` ，或生产者应用退出导致本地事务未提交任何状态。则消息队列RocketMQ版服务端会向消息生产者发起事务回查，第一次回查后仍未获取到事务状态，则之后每隔一段时间会再次回查。
		  a.回查间隔时间：系统默认每隔30秒发起一次定时任务，对未提交的半事务消息进行回查，共持续12小时。
		  b.第一次消息回查最快时间：该参数支持自定义设置。若指定消息未达到设置的最快回查时间前，系统默认每隔30秒一次的回查任务不会检查该消息。
		- 消费消息规则
		  事务消息的Group ID不能与其他类型消息的Group ID共用。与其他类型的消息不同，事务消息有回查机制，回查时消息队列RocketMQ版服务端会根据Group ID去查询生产者客户端。
	- RocketMQ事务消息实现流程
	  以RocketMQ 4.5.2版本为例，事务消息有专门的一个队列RMQ_SYS_TRANS_HALF_TOPIC，所有的prepare消息都先往这里放，当消息收到Commit请求后，就把消息再塞到真实的Topic队列里，供Consumer消费，同时向RMQ_SYS_TRANS_OP_HALF_TOPIC塞一条消息。简易流程图如下：
	  ![image.png](../assets/image_1656420471976_0.png)
	  RocketMQ事务消息实现流程
	  上述流程中，请允许我这样划分模块职责：
	  RocketMQ Client即我们工程中导入的依赖jar包，RocketMQ Broker端即部署的服务端，NameServer暂未体现。
	  应用模块成对出现，上游为事务消息生产端，下游为事务消息消费端（事务消息对消费端是透明的，与普通消息一致）。
	  
	  应用模块的事务因为中断，或是其他的网络原因，导致无法立即响应的，RocketMQ当做UNKNOW处理，RocketMQ事务消息还提供了一个补救方案：事务回查机制:定时查询事务消息的数据库事务状态
	  ![image.png](../assets/image_1656420581612_0.png)
- RocketMQ 的刷盘机制
	- 同步刷盘和异步刷盘
	  ![image.png](../assets/image_1656423261719_0.png)
	  同步刷盘:如上图所示，在同步刷盘中需要等待一个刷盘成功的 `ACK` ，同步刷盘对 `MQ` 消息可靠性来说是一种不错的保障，但是**性能上会有较大影响**，一般地适用于金融等特定业务场景。
	  异步刷盘:而异步刷盘往往是开启一个线程去异步地执行刷盘操作。消息刷盘采用后台异步线程提交的方式进行，**降低了读写延迟**，提高了 `MQ` 的性能和吞吐量，一般适用于如发验证码等对于消息保证要求不太高的业务场景。
	  一般地，**异步刷盘只有在 `Broker` 意外宕机的时候会丢失部分数据**，你可以设置 `Broker` 的参数 `FlushDiskType` 来调整你的刷盘策略(ASYNC_FLUSH 或者 SYNC_FLUSH)。
	- 同步复制和异步复制
	  上面的同步刷盘和异步刷盘是在单个结点层面的，而同步复制和异步复制主要是指的 `Borker` 主从集群模式下，主节点返回消息给客户端的时候是否需要同步从节点。
	  1. 同步复制： 也叫 “同步双写”，也就是说，**只有消息同步双写到主从节点上时才返回写入成功**。
	  2. 异步复制：**消息写入主节点之后就直接返回写入成功**。
	  然而，很多事情是没有完美的方案的，
	  好处:就比如我们进行消息写入的节点越多就更能保证消息的可靠性，
	  坏处:但是随之的性能也会下降，
	  所以需要程序员根据特定业务场景去选择适应的主从复制方案。
	- **异步复制会不会也像异步刷盘那样影响消息的可靠性呢？**
	  答案是不会的，因为两者就是不同的概念，对于消息可靠性是通过不同的刷盘策略保证的，而像异步同步复制策略仅仅是影响到了**可用性**。为什么呢？其主要原因**是 `RocketMQ` 是不支持自动主从切换的，当主节点挂掉之后，生产者就不能再给这个主节点生产消息了**。
	  异常场景分析:
	  比如这个时候采用异步复制的方式，在主节点还未发送完需要同步的消息的时候主节点挂掉了，这个时候从节点就少了一部分消息。但是此时生产者无法再给主节点生产消息了，**消费者可以自动切换到从节点进行消费**(仅仅是消费)，所以在主节点挂掉的时间只会产生主从结点短暂的消息不一致的情况，而当主节点重启之后，从节点那部分未来得及复制的消息还会继续复制。
	  
	  在单主从架构中，如果一个主节点挂掉了，那么也就意味着整个系统不能再生产了。那么这个可用性的问题能否解决呢？**一个主从不行那就多个主从的呗**
	  而在 `RocketMQ` 中采用了 `Dledger` 解决这个问题。他要求在写入消息的时候，要求**至少消息复制到半数以上的节点之后**，才给客⼾端返回写⼊成功，并且它是⽀持通过选举来动态切换主节点的。这里我就不展开说明了，读者可以自己去了解。
	  >也不是说 `Dledger` 是个完美的方案，至少在 `Dledger` 选举过程中是无法提供服务的，而且他必须要使用三个节点或以上，如果多数节点同时挂掉他也是无法保证可用性的，而且要求消息复制半数以上节点的效率和直接异步复制还是有一定的差距的。
- RocketMQ存储机制
  问题：在 `Topic` 中的**队列是以什么样的形式存在的？队列中的消息又是如何进行存储持久化的呢？**
  这里涉及到了 `RocketMQ` 是如何设计它的存储结构了。我首先想大家介绍 `RocketMQ` 消息存储架构中的三大角色—— `CommitLog` 、 `ConsumeQueue` 和 `IndexFile` 。`
	- CommitLog` ：
	  id:: 62bb06e8-8f7f-4eca-b7b0-9d513b1758e1
	  消息主体以及元数据的存储主体，存储 `Producer` 端写入的消息主体内容,消息内容不是定长的。单个文件大小默认1G ，文件名长度为20位，左边补零，剩余为起始偏移量，比如00000000000000000000代表了第一个文件，起始偏移量为0，文件大小为1G=1073741824；当第一个文件写满了，第二个文件为00000000001073741824，起始偏移量为1073741824，以此类推。消息主要是**顺序写入日志文件**，当文件满了，写入下一个文件。
	  在写入commitLog的时候内部会有一个mappedFile内存映射文件，消息是先写入到这个内存映射文件中，然后根据刷盘策略写到硬盘中。
	  ![image.png](../assets/image_1656425780236_0.png) 
	  为什么 `CommitLog` 文件要设计成固定大小的长度呢？提醒：**内存映射机制**。
	- `ConsumeQueue` ： 
	  消息消费队列，**引入的目的主要是提高消息消费的性能**(我们再前面也讲了)，
	  由于 `RocketMQ` 是基于主题 `Topic` 的订阅模式，消息消费是针对主题进行的。
	  如果要遍历 `commitlog` 文件中根据 `Topic` 检索消息是非常低效的。 `Consumer` 即可根据 `ConsumeQueue` 来查找待消费的消息。其中， `ConsumeQueue` （逻辑消费队列）**作为消费消息的索引**，保存了指定 `Topic` 下的队列消息在 `CommitLog` 中的**起始物理偏移量 `offset` **，消息大小 `size` 和消息 `Tag` 的 `HashCode` 值。
	  `consumequeue` 文件可以看成是基于 `topic` 的 `commitlog` 索引文件，故 `consumequeue` 文件夹的组织方式如下：topic/queue/file三层组织结构，具体存储路径为：$HOME/store/consumequeue/{topic}/{queueId}/{fileName}。同样 `consumequeue` 文件采取定长设计，每一个条目共20个字节，分别为8字节的 `commitlog` 物理偏移量、4字节的消息长度、8字节tag `hashcode` ，单个文件由30W个条目组成，可以像数组一样随机访问每一个条目，每个 `ConsumeQueue` 文件大小约5.72M(20*300000/1024/1024.0=5.72M)；
	  ![image.png](../assets/image_1656426210616_0.png)
	- `IndexFile` ： 
	  `IndexFile` （索引文件）提供了一种可以通过key或时间区间来查询消息的方法。这里只做科普不做详细介绍。
	- 总结来说，整个消息存储的结构，最主要的就是 `CommitLoq` 和 `ConsumeQueue` 。而 `ConsumeQueue` 你可以大概理解为 `Topic` 中的队列。
	  ![image.png](../assets/image_1656424562196_0.png)
	- 关于topic是否公用同一个commit log文件
	  `RocketMQ` 采用的是**混合型的存储结构**，即为 `Broker` 单个实例下所有的队列共用一个日志数据文件来存储消息。
	  有意思的是在同样高并发的 `Kafka` 中会为每个 `Topic` 分配一个存储文件。--->这就有点类似于我们有一大堆书需要装上书架， `RockeMQ` 是不分书的种类直接成批的塞上去的，而 `Kafka` 是将书本放入指定的分类区域的。
	  而 `RocketMQ` 为什么要这么做呢？原因是**提高数据的写入效率**，不分 `Topic` 意味着我们有更大的几率获取**成批**的消息进行数据写入，但也会带来一个麻烦就是读取消息的时候需要遍历整个大文件，这是非常耗时的。
	  所以，在 `RocketMQ` 中又使用了 `ConsumeQueue` 作为每个队列的索引文件来**提升读取消息的效率**。我们可以直接根据队列的消息序号，计算出索引的全局位置（索引序号*索引固定⻓度20） ((62bb06e8-8f7f-4eca-b7b0-9d513b1758e1)) ，然后直接读取这条索引，再根据索引中记录的消息的全局位置，找到消息。
	- RocketMQ存储架构图
	  ![image.png](../assets/image_1656425059030_0.png)
	  左边生产者表示一个生产者集群,右边消费者表示一个消费者组
	  首先，在最上面的那一块就是我刚刚讲的你现在可以直接**把 `ConsumerQueue` 理解为 `Queue` **。
	  在图中最左边说明了红色方块代表被写入的消息，虚线方块代表等待被写入的。左边的生产者发送消息会指定 `Topic` 、 `QueueId` 和具体消息内容，而在 `Broker` 中管你是哪门子消息，他直接**全部顺序存储到了 CommitLog**。而根据生产者指定的 `Topic` 和 `QueueId` 将这条消息本身在 `CommitLog` 的偏移(offset)，消息本身大小，和tag的hash值存入对应的 `ConsumeQueue` 索引文件中。而在每个队列中都保存了 `ConsumeOffset` 即每个消费者组的消费位置 ((62bac06c-eaa4-4c39-97ea-8119cb35bb69)) ，而消费者拉取消息进行消费的时候只需要根据 `ConsumeOffset` 获取下一个未被消费的消息就行了。
	  
	  广播模式下，同消费组的消费者相互独立，消费进度(ConsumeOffset)要单独存储;
	  集群模式下，同一条消息只会被同一个消费组消费一次，消费进度(ConsumeOffset)会参与到负载均衡中，故消费进度是需要共享的。
	  广播模式：DefaultMQPushConsumer的BROADCASTING模式，各个Consumer没有互相干扰，使用LoclaFileOffsetStore，把Offset存储在Consumer本地。
	  集群模式：DefaultMQPushConsumer的CLUSTERING模式，由Broker端存储和控制Offset的值，使用RemoteBrokerOffsetStore。
	  
	  上述就是我对于整个消息存储架构的大概理解(这里不涉及到一些细节讨论，比如稀疏索引等等问题)
- 资料
  [阿里云 事务消息](https://help.aliyun.com/document_detail/43348.html)
  博客
  [通过这三个文件彻底搞懂Rocketmq的存储原理](https://developer.51cto.com/article/685836.html)
  源码解读分析
  [MappedByteBuffer的一点优化](https://lishoubo.github.io/2017/09/27/MappedByteBuffer%E7%9A%84%E4%B8%80%E7%82%B9%E4%BC%98%E5%8C%96/)
-