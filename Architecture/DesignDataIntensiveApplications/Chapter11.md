# Stream 流处理

Learn Amazon Kinesis

有效的复杂系统总是从简单的系统演化而来。 反之亦然：从零设计的复杂系统没一个能有效工作的。
——约翰·加尔，Systemantics（1975）

## Transmitting Event Streams (传递事件流)

### Messaging System 消息系统
​向消费者通知新事件的常用方式是使用消息传递系统（messaging system）：生产者发送包含事件的消息，然后将消息推送给消费者。一个消息传递系统允许多个生产者节点将消息发送到同一个主题，并允许多个消费者节点接收主题中的消息。

1. 如果生产者发送消息的速度比消费者能够处理的速度快会发生什么？一般来说，有三种选择：系统可以丢掉消息，将消息放入缓冲队列，或使用背压（backpressure）
2. 如果节点崩溃或暂时脱机，会发生什么情况？ —— 是否会有消息丢失？与数据库一样，持久性可能需要写入磁盘和/或复制的某种组合

**直接从生产者传递给消费者**

**消息代理**
一种广泛使用的替代方法是通过消息代理（message broker）（也称为消息队列（message queue））发送消息，消息代理实质上是一种针对处理消息流而优化的数据库。它作为服务器运行，生产者和消费者作为客户端连接到服务器。生产者将消息写入代理，消费者通过从代理那里读取来接收消息。

通过将数据集中在代理上，这些系统可以更容易地容忍来来去去的客户端（连接，断开连接和崩溃），而持久性问题则转移到代理的身上。一些消息代理只将消息保存在内存中，而另一些消息代理（取决于配置）将其写入磁盘，以便在代理崩溃的情况下不会丢失。针对缓慢的消费者，它们通常会允许无上限的排队（而不是丢弃消息或背压），尽管这种选择也可能取决于配置。

排队的结果是，消费者通常是**异步（asynchronous）**的：当生产者发送消息时，通常只会等待代理确认消息已经被缓存，而不等待消息被消费者处理。向消费者递送消息将发生在未来某个未定的时间点 —— 通常在几分之一秒之内，但有时当消息堆积时会显著延迟。

**消息代理与数据库对比**
​有些消息代理甚至可以使用XA或JTA参与两阶段提交协议（参阅“实践中的分布式事务”）。这个功能与数据库在本质上非常相似，尽管消息代理和数据库之间仍存在实践上很重要的差异：
* 数据库通常保留数据直至显式删除，而大多数消息代理在消息成功递送给消费者时会自动删除消息。这样的消息代理不适合长期的数据存储。
* 由于它们很快就能删除消息，大多数消息代理都认为它们的工作集相当小—— 即队列很短。如果代理需要缓冲很多消息，比如因为消费者速度较慢（如果内存装不下消息，可能会溢出到磁盘），每个消息需要更长的处理时间，整体吞吐量可能会恶化。
* 数据库通常支持二级索引和各种搜索数据的方式，而消息代理通常支持按照某种模式匹配主题，订阅其子集。机制并不一样，对于客户端选择想要了解的数据的一部分，这是两种基本的方式。
* 查询数据库时，结果通常基于某个时间点的数据快照；如果另一个客户端随后向数据库写入一些改变了查询结果的内容，则第一个客户端不会发现其先前结果现已过期（除非它重复查询或轮询变更）。相比之下，消息代理不支持任意查询，但是当数据发生变化时（即新消息可用时），它们会通知客户端。

#### 多个消费者
**负载均衡（load balance）**
每条消息都被传递给消费者之一，所以处理该主题下消息的工作能被多个消费者共享。代理可以为消费者任意分配消息。当处理消息的代价高昂，希望能并行处理消息时，此模式非常有用（在AMQP中，可以通过让多个客户端从同一个队列中消费来实现负载均衡，而在JMS中则称之为共享订阅

**扇出（fan-out**
​ 每条消息都被传递给所有消费者。扇出允许几个独立的消费者各自“收听”相同的消息广播，而不会相互影响 —— 这个流处理中的概念对应批处理中多个不同批处理作业读取同一份输入文件 （JMS中的主题订阅与AMQP中的交叉绑定提供了这一功能）。

**确认与重新交付**
​消费随时可能会崩溃，所以有一种可能的情况是：代理向消费者递送消息，但消费者没有处理，或者在消费者崩溃之前只进行了部分处理。为了确保消息不会丢失，消息代理使用确认（acknowledgments）：客户端必须显式告知代理消息处理完毕的时间，以便代理能将消息从队列中移除。即使消息代理试图保留消息的顺序（如JMS和AMQP标准所要求的），负载均衡与重传的组合也不可避免地导致消息被重新排序。为避免此问题，你可以让每个消费者使用单独的队列（即不使用负载均衡功能）。如果消息是完全独立的，则消息顺序重排并不是一个问题。但正如我们将在本章后续部分所述，如果消息之间存在因果依赖关系，这就是一个很重要的问题。

### Partitioned Logs
既有数据库的持久存储方式，又有消息传递的低延迟通知？这就是基于日志的消息代理（log-based message brokers） 背后的想法。

#### 使用日志进行消息存储
为了扩展到比单个磁盘所能提供的更高吞吐量，可以对日志进行分区.不同的分区可以托管在不同的机器上，且每个分区都拆分出一份能独立于其他分区进行读写的日志。

Apache Kafka 【17,18】，Amazon Kinesis Streams 【19】和Twitter的DistributedLog 【20,21】都是基于日志的消息代理。 Google Cloud Pub/Sub在架构上类似，但对外暴露的是JMS风格的API，而不是日志抽象【16】。尽管这些消息代理将所有消息写入磁盘，但通过跨多台机器分区，每秒能够实现数百万条消息的吞吐量，并通过复制消息来实现容错性

## Databases and Streams
https://github.com/Vonng/ddia/blob/master/ch11.md#%E6%B5%81%E4%B8%8E%E6%95%B0%E6%8D%AE%E5%BA%93
### Keeping Systems in Sync

### Change Data Capture

### Event Sourcing

### State, Streams and Immutability


## Processing Streams

### Uses of Stream Processing

### Reasoning About Time

### Stream Joins

### Fault Tolerance

## Summary
