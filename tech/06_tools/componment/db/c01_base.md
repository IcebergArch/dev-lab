# base

base 是分布式、面向列的开源数据库（其实准确的说是面向列族）。
HDFS为Hbase提供可靠存储服务
MapReduce为Hbase提供高性能计算能力
ZK为HBase提供稳定性和filover机制

可以通过大量廉价的机器解决海量数据高性能读写

## component

### base

Column Family

又叫列族，Hbase 通过列族划分数据的存储，列族下面可以包含任意多的列

Rowkey

概念和 mysql 中的主键是完全一样的，Hbase 使用 Rowkey 来唯一的区分某一行的数据。

Region 

概念和关系型数据库的分区或者分片差不多。

TimeStamp

是Hbase多版本的关键，在 Hbase 中使用不同的 timestame 来标识相同rowkey 行对应的不通版本的数据。用户指定timestamp后可读取历史数据

![](../images/hbase.png)

### region 寻址
第 1 步：Client 请求 ZK 获取.META.所在的 RegionServer 的地址。
第 2 步：Client 请求.META.所在的 RegionServer 获取访问数据所在的 RegionServer 地
址，client 会将.META.的相关信息 cache 下来，以便下一次快速访问。
第 3 步：Client 请求数据所在的 RegionServer，获取所需要的数据。


### hbase write
获取 RegionServer
第 1 步：Client 获取数据写入的 Region 所在的 RegionServer
请求写 Hlog
第 2 步：请求写 Hlog, Hlog 存储在 HDFS，当 RegionServer 出现异常，需要使用 Hlog 来
恢复数据。
请求写 MemStore
第 3 步：请求写 MemStore,只有当写 Hlog 和写 MemStore 都成功了才算请求写入完成。
MemStore 后续会逐渐刷到 HDFS 中。
![](../images/hbase_write.png)