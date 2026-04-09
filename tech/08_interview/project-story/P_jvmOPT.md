# JVM 调优

1. 垃圾收集器选择：
    G1 GC：适合大堆、低停顿
    Parallel GC：吞吐优先
    CMS（JDK 8）：老年代压力较大时可考虑

2. G1:Minor GC 参数优化：
    -XX:MaxGCPauseMillis=200      # G1 GC 期望停顿
    -XX:G1HeapRegionSize=16m      # Eden 区粒度

3. 监控对象晋升率：
    -XX:+PrintGCApplicationStoppedTime -XX:+PrintGCDetails
    如果对象频繁晋升 Old Gen → 新生代调整不足或对象生命周期估计有误