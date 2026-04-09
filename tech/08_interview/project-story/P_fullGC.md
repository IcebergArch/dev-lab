## 频繁 Full GC 排查流程（text版）

问题现象：

系统明显卡顿
接口 RT 抖动严重
偶发长时间停顿（秒级）
Step 1：确认 Full GC 是否频繁
jstat -gcutil <pid> 1000

观察：

FGC 次数持续增加
FGCT 时间持续增长
Old Gen 使用率接近 100%

判断：

Full GC 频繁
Step 2：查看 GC 日志
grep "Full GC" gc.log

观察：

Full GC 触发频率
Full GC 停顿时间

典型表现：

Pause Full GC 3~10s

说明：

老年代压力大
Step 3：检查 Old Gen 使用情况
jmap -heap <pid>

观察：

Old Gen 使用率很高
GC 后下降不明显

说明：

对象无法回收

可能：

内存泄漏
缓存过大
大对象问题
Step 4：导出 Heap Dump
jmap -dump:format=b,file=heap.hprof <pid>

然后：

使用 MAT 打开 heap.hprof
Step 5：分析大对象

进入：

Dominator Tree

按：

Retained Size 排序

观察：

哪些对象占用最多内存

重点检查：

Map
List
缓存对象
Session 对象
Step 6：定位代码问题

常见根因：

静态 Map 未清理
缓存未设置过期
ThreadLocal 未 remove
连接未关闭

典型代码问题：

无限增长缓存
长期持有对象引用
Step 7：修复代码

优化方向：

增加缓存过期
限制缓存大小
及时释放对象
避免静态集合无限增长

典型方案：

使用带 TTL 的缓存
设置最大容量
Step 8：验证优化效果

再次观察：

jstat -gcutil <pid>

目标：

FGC 减少
Old Gen 稳定
系统不卡顿