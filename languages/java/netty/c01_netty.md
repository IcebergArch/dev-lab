# netty

高性能NIO框架，支持TCP、UDP与文件读写的异步非阻塞，使用方可以主动或被动获取IO就绪信息。



## features

Netty基于Reactor模式设计实现
- 零拷贝，使用堆外直接内存RW数据，规避一次向JVM堆内内存的COPY
- 内存池，基于内存池的缓冲区重用策略
- 高效Reactor线程模型

## Reactor模型

### 单线程模型
    作为 client-netty-server 中继层，1个线程，即接收 client 的请求，也发起向 server 的链接
### 多线程模型
    作为 client-netty-server 中继层，1个线程负责收发事件处理，一组线程负责事实际IO(读写、编码、发送)
### 主从Reactor模型
    作为 client-netty-server 中继层，一组NIO线程；
    Acceptor 负责客户端的登录、注册、安全认知，通过后将其分发到 NIO-线程池中，由NIO线程负责实际的IO（编解码、收发）

## LOCk

- 无锁化，单线程内部串行化，避免竞争

## 高性能序列化

默认采用google protobuf，可通过自定义接口切换为其他的编解码框架
- 小包封大包，减少通信包频次
- 开启RPS，在 CPU mask 内倾向于负载低的核处理 SoftIRQ → 大部分情况下，EventLoop 所在核可能处理该包 → 数据本地化概率高
    > 基于数据包hash( calc[src_ip&dest_ip&dest_port])选择CPU
- CPU pinning：EventLoop 固定核 → 避免线程迁移，提高 TLB/cache 命中率

### 流程

1️⃣ 系统与 IOMMU 初始化
```text
OS 启动
   ↓
探测 IOMMU 硬件
   ↓
初始化 IOMMU 驱动
   ↓
CPU 写入 IOMMU 映射表 (iommu_map)
   - 设备ID -> IOVA -> 物理内存
   - 设置权限 (读/写)
```

硬件：IOMMU 固定存在

映射表：每次由 CPU/驱动动态配置

CPU 仅配置规则，不搬运数据

2️⃣ 网卡驱动初始化
```text
网卡驱动加载
   ↓
分配 DMA buffer（主内存）
   ↓
调用 iommu_map() 建立映射
   ↓
网卡 DMA 就绪
```

硬件：网卡 DMA 引擎准备写入内存

CPU：短暂配置，不占用 IO 数据搬运

3️⃣ 数据接收阶段（DMA 写内存 + SoftIRQ）
```text
网络包到达网卡
   ↓
网卡 DMA 写数据到内存 buffer
   ↓
IOMMU 校验地址和权限（硬件完成）
   ↓
SoftIRQ 标记 RX buffer ready
```

CPU 不轮询数据

CPU 仅在 SoftIRQ 被触发时短暂介入

内存 buffer 已经写入，可能触发 L1/L2 cache 填充

4️⃣ RPS（Receive Packet Steering）分发
```text
SoftIRQ -> 内核判断负载
   ↓
高概率分发到某 CPU 处理
   - 尽量和 Netty EventLoop CPU 对齐
   - 减少跨 CPU cache/memory bounce
```

RPS：事件驱动，不是主动轮询

CPU 参与短暂数据处理

主要目的是 优化 cache 命中

5️⃣ 用户态消费（Netty NIO Thread）
```text
EventLoop / NIO-thread 被唤醒
   ↓
read() 或 zero-copy 访问内核 buffer
   ↓
拷贝或直接操作数据到用户态
   ↓
执行 handler / 业务逻辑
```

CPU 占用短暂、事件驱动

业务逻辑处理完毕，线程可调度去处理其他任务