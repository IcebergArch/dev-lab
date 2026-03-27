# MySQL

## InnoDB

支持事务的数据库存储引擎

### 结构

1. 线程管理

<b>InnoDB内部（master + Purge + Page Cleaner）+ InnoDB外部 （IO搞定与磁盘的读写）</b>

- Master thread：
    主loop，后台backgroup loop，刷新flush loop，暂停suspend loop），主线程依据运行状态在各个循环间切换 
- IO thread
    4 * write, 4 * read, insert buffer, log IOthread
- purge thread
    已提交事务的 undo log, 回收已经被使用的undo页
- Page Cleaner Thread
    脏页的刷新

2. 内存管理

<b>data buffer + log buffer + ext buffer</b>

- data buffer ： 主要用于存放数据页和索引页
缓冲池(多个页，page.defaultSize=16kb)：页如果在缓冲池中，则读写都优先操作缓冲池中的页
    三组列表维护：
    - [LRU List] 最近使用的页
    - [Free List] 未使用或已清理
    - [Flush List] 待回刷的 dirty pages（脏页，可能同时还在 LRU 中）

- log buffer ：
    redo log buffer

- 额外内存池
    有些 InnoDB 内部数据结构（如锁结构、插入缓冲区、LRU 链表节点等）也需要内存，如果直接从缓冲池里分配，会占用缓存页空间 → 缓冲池命中率下降

3. 优化

AIO
    异步IO
缓冲池
    优化数据读写
logBuffer
    优化log写
CheckPoint
    恢复时，避免从文件头开始
Insert Buffer + Merge + Flush + Double Write
    优化非聚簇索引更新时的频繁IO
    （非聚簇索引，相邻节点的页并不是连续的）
    （IO场景：页不在缓冲池要IO，页改了需要IO回磁盘，页满了分裂导致多页IO，多个索引都变更多个IO）
    Double Write
        防止系统 crash 导致单页写入只写了一半 → 数据页损坏
        Crash 后通过 double write buffer 可以恢复完整页