# kafka

Kafka 是一种高吞吐量、分布式、基于发布/订阅的消息系统，最初由 LinkedIn 公司开发，使用Scala 语言编写，目前是 Apache 的开源项目。
1. broker：Kafka 服务器，负责消息存储和转发
2. topic：消息类别，Kafka 按照 topic 来分类消息
3. partition：topic 的分区，一个 topic 可以包含多个 partition，topic 消息保存在各个partition 上
4. offset：消息在日志中的位置，可以理解是消息在 partition 上的偏移量，也是代表该消息的唯一序号
5. Producer：消息生产者
6. Consumer：消息消费者
7. Consumer Group：消费者分组，每个 Consumer 必须属于一个 group
8. Zookeeper：保存着集群 broker、topic、partition 等 meta 数据；另外，还负责 broker 故障发现，partition leader 选举，负载均衡等功能

## features

broker: server

logic:

producer + topic + consumer

topic save:
message
 => index + messageSize + data
 => segment
 => segment.index + N * segmentN.log

Get 1 message:
1. search segment.index, get {index}
2. get segment.log
3. get index + messageSize, get {message-data}


## producer

写负载均衡：利用partition同步机制，分散写流量，写流量分发使用负载均衡策略
批量写：聚合多个message写为一个，减少brokerIO次数
发送压缩后的消息：吃CPU资源

## consumer

partition 内部有序
