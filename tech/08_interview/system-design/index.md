# 系统设计

原则：时间换空间，空间换时间

## 架构设计

技术选型（text版）
服务：

小规模 / 简单业务 → 单体
    原因：开发成本低、部署简单、事务一致性强，适合业务复杂度不高的场景
高并发 / 复杂业务 → 分布式（微服务）
    原因：支持横向扩展、服务解耦，能应对高并发和复杂业务，但引入分布式复杂性

语言层面：

核心业务 → Java
    原因：生态成熟（Spring体系）、稳定性高，适合复杂业务和长期维护
高并发 / 基础服务 → Go
    原因：协程模型轻量、并发性能好、资源占用低，适合高吞吐场景
数据处理 / AI → Python
    原因：开发效率高，数据分析和AI生态完善（如机器学习库）

中间件选型：

ID生成器：
自研（雪花算法（主流））
    原因：保证全局唯一、趋势递增、无中心依赖，性能高
采买
    原因：减少开发成本，但灵活性和可控性较低

分布式锁：

Redis（常规）
    原因：性能高、实现简单，适合大多数业务场景
Zookeeper（强一致）
    原因：基于CP模型，强一致性保证更好，适用于对锁可靠性要求极高的场景

缓存：Redis
    原因：内存存储、读写性能高，适合高频访问数据

    穿透 → 布隆过滤器
    原因：拦截不存在的数据请求，防止直接打到数据库

    击穿 → 加锁
    原因：热点key失效时，避免大量请求同时访问数据库

    雪崩 → 随机过期
    原因：避免大量key同时过期导致数据库压力激增

消息队列：

高吞吐 → Apache Kafka
    原因：顺序写磁盘、分区机制，吞吐量极高，适合日志和流处理
交易系统 → Apache RocketMQ
    原因：支持事务消息、顺序消息、延迟消息，适合订单、支付等业务
简单异步 → RabbitMQ
    原因：实现简单、延迟低，适合中小系统

搜索引擎：Elasticsearch
    原因：基于倒排索引，支持全文检索和高效查询，适合搜索和日志分析

存储：

核心数据 → MySQL
    原因：支持事务（ACID），保证数据一致性，适合核心业务数据
缓存 → Redis
    原因：提升读性能，降低数据库压力
文档 → MongoDB
    原因：Schema灵活，适合非结构化或半结构化数据
分析 → Hive / ClickHouse
    原因：支持大规模数据分析，适合离线或OLAP场景

## 常见设计模式
策略模式
工厂模式
责任链模式
模板方法模式
观察者模式



## 排序(大文件、小内存)

思路：归并
1. 大文件拆小文件; 小文件快排；
2. 小文件merge, 最小堆，每个文件读一个；最小值输出到结果文件，而后该文件再往后读
3. 结果文件完整且有序

## 查找(大文件中重复url)

思路：分桶查找
1. hash散列url到不同文件，让相似的URL整理到一起
2. loop单文件获取重复
3. 所有的重复都找到了

## 大文件中热点关键词统计

思路：分桶 + HashMap + 堆

1. 根据关键词哈希分桶到小文件
2. 每个桶统计频次，去重并取局部热点 Top 10
3. 合并所有桶局部 Top 10，用最小堆得到全局热点 Top 10

## 十亿个数中的中位数

1. 分桶缩小范围，每个数字开始的五个比特分桶，统计桶的数量就知道中位数在哪个桶
    继续分桶，直到可以放入内存
2. 对于桶内数据
    快排（找第K小）、最大堆都可以找到 目标中位数

## 顺序打印ABC
    public static void main(String[] args) {

        Semaphore sa = new Semaphore(1);
        Semaphore sb = new Semaphore(0);
        Semaphore sc = new Semaphore(0);

        Thread ta = new Thread(() -> print("A", sa, sb));
        Thread tb = new Thread(() -> print("B", sb, sc));
        Thread tc = new Thread(() -> print("C", sc, sa));

        ta.start();
        tb.start();
        tc.start();
    }

    private static void print(String s, Semaphore cur, Semaphore next) {
        while (true) {
            try {
                cur.acquire();
                System.out.println(s);
                next.release();
            } catch (Exception e){
                System.out.println("Error");
            }
        }
    }
