# Kafka 设计原理

## Kafka是如何进行集群管理的？

Kafka依赖ZooKeeper进行集群管理。每个broker启动时，会将自己注册到ZooKeeper下的一个临时节点，同时还会创建一个监听器监听该临时节点的状态。一旦broker启动成功后，监听器会自动同步整个集群信息到该broker上；而一旦broker崩溃，它与ZooKeeper的会话就会失效，导致临时节点被删除，监听器被触发，然后处理broker崩溃的后续事宜。这就是Kafka管理集群及其成员的主要流程。

## ISR是如何设计的？

ISR是Kafka集群动态维护的一组同步副本集合（in-sync replicas）。每个topic分区都有自己的ISR列表，ISR中的所有副本都与leader保持同步状态。leader副本总是包含在ISR中，只有ISR中的副本才有资格被选举为leader。prducer写入的一条消息Kafka只有被ISR中的所有副本都接收到，才被视为“已提交”状态。由此可见，如果ISR中有N个副本，那么该分区最多可以忍受N-1个副本崩溃而不丢失已提交消息。

## 副本水印

每个Kafka副本对象都持有两个重要属性：

* 日志末端位移（log end offset，LEO），记录该副本底层日志文件中下一条消息的位移值。
* 高水印（HW），保存副本最新一条已提交消息的位移。任何一个副本的HW一定不大于其LEO，而小于或等于HW值的所有消息被认为是“已提交的”或“已备份的”。

任意时刻，HW指向的是实实在在的消息，而LEO总是指向下一条待写入消息，也就是LEO指向的位置上是没有消息的。

消费者无法消费分区leader副本上那些唯一大于分区HW的消息。

LEO如何更新呢？

首先，是follower副本的LEO何时更新。follower副本的LEO有两个，一个是follower副本所在的broker缓存上，另一个是leader副本所在broker的缓存上。前者帮助follower副本自身更新HW值，后者用来确定leader副本的HW值，即分区HW。

follower副本端的LEO就是该副本底层日志的LEO，也就是说，每写入一条消息，其LEO值会加1。

leader部分端的follower副本LEO更新发生在leader处理follower的FETCH请求时，一旦leader接收到follower发送的FETCH请求，它首先会从自己的log中读取响应的数，但是在给follower返回数据之前它先去更新follower的LEO。

leader副本更新LEGO的机制和时机同follower更新LEO类似，leader写log时就会自动更新它自己的LEO值。

HW如何更新？

follower更新HW发生在其更新LEO之后，一旦follower想log写完数据，它就会尝试更新HW值。具体做法就是比较当前LEO值与FETCH响应中leader的HW值，取两者小的作为新的HW值。因此，如果follower的LEO值超过了leader的HW值，那么follower的HW值是不会越过leader的HW值。

leader在以下四种情况下会尝试更新HW：

* 副本成为leader副本时
* broker出现崩溃导致副本被踢出ISR时
* producer向leader副本写入消息时
* leader处理follower的FETCH请求时

leader尝试确定分区HW时，它会选出所有follower副本的LEO以及自己的LEO，比较他们并选择最小的LEO值作为HW值。