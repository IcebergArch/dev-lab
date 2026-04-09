## Minor GC

一、频繁 GC（Minor GC 频繁）排查流程（text版）

问题现象：

系统 CPU 持续偏高
接口 RT 变慢
GC 次数明显增加
系统整体吞吐下降

Step 1：确认 GC 是否频繁
    jstat -gcutil <pid> 1000

    观察指标：

    YGC（Young GC 次数）增长很快
    YGCT（Young GC 时间）持续增加
    Eden 使用率快速上涨

    判断：

    Minor GC 频繁

Step 2：查看 GC 日志
    grep "Pause Young" gc.log

    观察：

    Young GC 触发间隔很短
    每次回收时间较短（例如 10~50ms）
    但触发频率很高

    重点关注：

    GC 触发频率
    Eden 区使用情况
    对象分配速率

Step 3：分析对象分配速率

    使用：

    jstat -gc <pid> 1000
    ===>
    S0     S1     E      O      M     CCS    YGC   YGCT   FGC   FGCT     GCT
    0.00  100.00  82.11  94.78  99.00 96.21  1587  48.7    20   16.4    65.1

    观察：

    Eden 区增长非常快
    Survivor 使用率持续变化

    说明：

    短生命周期对象大量创建

Step 4：检查代码对象创建热点

    使用：

    jmap -histo <pid>
    ===>
            num     #instances         #bytes  class name
        ----------------------------------------------
        1:        550000       132000000  [C
        2:        470000       75200000   java.lang.String
        3:        310000       74400000   [B
        4:         55000       88000000   com.example.cache.UserSession
        5:         25000       80000000   java.util.HashMap$Node
        6:         32000       51200000   java.util.ArrayList
        7:         12000       38400000   java.util.concurrent.ConcurrentHashMap$Node
        8:         6000       19200000   java.lang.Integer
        9:         2500       16000000   com.example.service.Order
        10:         1200        9600000   java.util.concurrent.ThreadPoolExecutor$Worker
        ...
        Total  = 2,678,900 objects

    查看：

    对象数量最多的类
    如：

    byte[]
    char[]
    String
    HashMap
    JSON 对象

    重点关注：

    短命对象

Step 5：定位代码热点

    常见问题：

    频繁 JSON 序列化
    字符串拼接
    大量 new 对象
    临时集合创建

    典型代码问题：

    循环内创建大量对象
    字符串使用 + 拼接
    频繁创建大 List
Step 6：优化代码

    优化方向：

    减少对象创建
    复用对象
    优化字符串拼接
    减少临时集合

    典型优化：

    String → StringBuilder
    对象复用
    缓存复用

Step 7：调整 JVM 参数（必要时）

    优化：

    -Xmn 调大新生代
    -XX:NewRatio 调整新生代比例

    目标：

    减少 Minor GC 触发次数
    Step 8：验证优化效果

    再次执行：

    jstat -gcutil <pid> 1000

    观察：

    YGC 频率下降
    CPU 降低
    RT 恢复