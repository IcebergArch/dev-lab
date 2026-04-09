# Slow SQL

慢 SQL 导致事务堆积排查流程（text版）

这是最常见线上事故之一。

    典型现象
    接口 RT 持续上升
    数据库连接数接近上限
    线程池堆积
    系统吞吐下降

可能伴随：
    GC 频繁
    CPU 上升

Step 1：查看线程堆栈
    jstack <pid>
    观察：
        大量线程处于：
            BLOCKED
            WAITING
            TIMED_WAITING

    重点看：数据库调用线程
    典型：等待数据库返回

Step 2：查看数据库连接池状态

    如果使用：

        HikariCP
        Druid

    查看：
        active connections
        waiting threads

    观察：

        active 接近最大值
        waiting 很多

    说明：连接池耗尽


Step 3：查看数据库慢 SQL

    执行：

        SHOW PROCESSLIST;

        或：

        SHOW FULL PROCESSLIST;

    观察：执行时间长的 SQL

    重点：执行时间 > 1s 的 SQL

Step 4：查看慢查询日志

    开启：

    slow_query_log = ON

    查看：slow query log

重点关注：

    扫描行数多
    执行时间长

Step 5：分析 SQL 执行计划

    执行：
        EXPLAIN SQL;

    观察：
        type 是否为 ALL
        是否使用索引
        扫描行数

    典型问题：
        全表扫描
        索引失效
        多表 Join 不合理

Step 6：优化 SQL

    优化方向：

    增加索引
    优化查询条件
    减少 Join
    分页查询

    典型优化：

    建立联合索引
    避免 select *
    限制返回数据量

Step 7：检查事务是否过长

    观察：

    长时间未提交事务

    可能问题：

    业务逻辑过重
    事务范围过大

    优化：

    缩小事务范围
    减少锁时间
    Step 8：验证系统恢复

    观察：

    数据库连接恢复
    线程堆积消失
    RT 恢复正常

    最终确认：

    问题解决