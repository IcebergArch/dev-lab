# MySQL

## 一条写SQL执行

SQL 语法解析
    SQL 语句经过 解析器 → 生成 语法树 → 决定操作类型（SELECT / UPDATE / DELETE / INSERT）
    判断涉及的表、字段、索引、条件
查找数据
    基于索引定位目标行：
        聚簇索引（Clustered Index）：直接定位数据行
        非聚簇索引（Secondary Index）：先查索引叶子节点得到 PK，再访问聚簇索引页定位真实行
        数据可能分散在不同页、不同叶子节点，B+ 树保证逻辑顺序
    从磁盘加载到：
        Buffer Pool
执行 & 加锁
    加锁位置（内存结构）：
    Record Lock → 聚簇索引叶子节点上对应记录
    Gap Lock / Next-Key Lock → 聚簇索引叶子节点中目标记录的 左邻居 + 当前记录 逻辑区间
    非聚簇索引只用来查找，不加锁
    所有锁都是 内存 LOCK_STRUCT，挂在索引页或 Gap 上

执行流程：

    单行更新（UPDATE single row）：

        定位行 → 加 Record Lock + 左侧 Gap Lock（Next-Key Lock）
        执行写操作 其中：两阶段提交（2PC） + WAL 原则，事务 提交成功的标志就是 redo log 刷盘
            
            事务执行: UPDATE user SET age=20 WHERE id=1;
                Undo 修改
                    生成 undo log（undo log buffer）
                    写入 undo 页（Buffer Pool）
                    redo log 记录 undo 页变化
                数据修改
                    修改数据页（Buffer Pool）
                    redo log 记录数据页变化
                事务提交
                    redo log commit + fsync
                    写 binlog
                    commit 返回成功
                    数据页和 undo 页后台异步刷盘(如果crash recovery，则从检查点开始重做commit的事务)
        事务结束（若无显式事务，InnoDB 自动启动隐式事务） → 解锁

    多行更新（for UPDATE multi row / 范围内单条多次更新）：

        启动隐式事务（若没有显式 BEGIN）
        对命中的每行：
            B+ 树定位 → 聚簇索引行
            加 Record Lock + 左侧 Gap Lock
            执行更新 → 生成 redo / undo log
            事务提交 / 回滚 → 批量释放所有锁

    范围更新（UPDATE multi row where xxx > X and xxx < Y

        命中索引，对范围数据加锁，一次加锁 所有的 record + gap
        未命中，锁表


锁的作用与释放
    Record Lock：保证同一行不会被其他事务修改
    Gap Lock：防止其他事务在间隙插入新行 → 防幻读
    Next-Key Lock = Record + 左侧 Gap → 默认 Repeatable Read
    锁释放时机：事务提交或回滚


## 一条读SQL执行

1. 语法解析
2. 非聚簇 -> 聚簇
3. 查 buffer pool, 没有则IO，然后更新到buffer pool
4. 返回结果

## Q&A

mysql锁，锁的是什么？按照record粒度存储的锁信息，位置在内存

mysql锁为何用 leftGAP + record?  左右GAP都可以，但是 数据量上已有数据 < 表可存储数据, 写操作的区间往往也在已有数据里，因此选择锁左边以拿到更好的性能