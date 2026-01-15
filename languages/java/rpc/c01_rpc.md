# RPC
remote procedure call 远程过程调用

## why

## now?
Protobuf、Thrift、Avro 等成熟的序列化解决方案来搭建 RPC 框架

## process

client 发起多个调用，接收结果，处理结束

1. 发起调用, 生成 requestID 区分不同线程的调用，一般用AtomicLong
2. 将处理结果的回调对象 callback ，存放到全局 ConcurrentHashMap 里 面
put(requestID, callback)；
3. 当线程调用 channel.writeAndFlush()发送消息后，紧接着执行 callback 的 get()方法试图获取远程返回的结果。在 get()内部，则使用 synchronized 获取回调对象 callback 的锁，再先检测是否已经获取到结果，如果没有，然后调用 callback 的 wait()方法，释放callback 上的锁，让当前线程处于等待状态。
4. 服务端接收到请求并处理后，将 response 结果（此结果中包含了前面的 requestID）发送给客户端，客户端 socket 连接上专门监听消息的线程收到消息，分析结果，取到requestID ，再从前面的 ConcurrentHashMap 里 面 get(requestID) ，从而找到callback 对象，再用 synchronized 获取 callback 上的锁，将方法调用结果设置到callback 对象里，再调用 callback.notifyAll()唤醒前面处于等待状态的线程。

问题：
1. 怎么暂停，等callBack再执行
2. 多线程调用，怎么保障结果分配正确
 responseA -> threadA, responseB -> threadB

## product

### protobuf

Protocol Buffer 的序列化 & 反序列化简单 & 速度快的原因是：
1. 编码 / 解码 方式简单（只需要简单的数学运算 = 位移等等）
2. 采用 Protocol Buffer 自身的框架代码 和 编译器 共同完成
Protocol Buffer 的数据压缩效果好（即序列化后的数据量体积小）的原因是：
1. a. 采用了独特的编码方式，如 Varint、Zigzag 编码方式等等
2. b. 采用 T - L - V 的数据存储方式：减少了分隔符的使用 & 数据存储得紧凑

### thrift
为什么要 Thrift：
1、多语言开发的需要 2、性能问题

