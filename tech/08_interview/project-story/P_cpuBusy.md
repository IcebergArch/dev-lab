# CPU Busy


一、确认 CPU 高负载
    操作系统层面：
    top / htop / sar
    查看哪个进程占用 CPU
    确认 Java 进程是否真正消耗 CPU
    JVM 层面：
    jstack <pid> > threads.txt
    检查线程状态：RUNNABLE 多吗？
    RUNNABLE 线程占 CPU → 可能是计算密集型或锁竞争
二、定位 CPU 热点
    1️⃣ 使用采样分析 Profiling 工具
    VisualVM / Mission Control / YourKit / JProfiler
    打开 CPU Profiling → 记录 方法调用占用 CPU 时间
    找出占用最多的函数或类

    示例：

    com.example.service.OrderProcessor.processOrder()  → 40% CPU
    java.util.concurrent.ThreadPoolExecutor$Worker.run() → 20% CPU
    JSON 序列化 / 正则匹配 / 大循环  → 高 CPU
    2️⃣ 结合 jstack 分析线程栈
    RUNNABLE 线程堆栈重复出现 → 热点代码
    BLOCKED / WAITING 线程占用少 → 不是锁问题

## JVM调优

1. JVM 调优方向
减少 CPU 在 GC 上消耗：
-XX:+UseG1GC -XX:MaxGCPauseMillis=200
-XX:+UseParallelGC -XX:ParallelGCThreads=<CPU核数>

2. 控制线程池大小：
CPU 核心数 = 线程池核心线程数 × 1~2（IO 密集型可多）
避免线程过多导致上下文切换占用 CPU

3. 异步 / 批处理优化：
批量处理数据，减少频繁调用
异步执行耗时任务