# executor

## 上下文切换
1. 保存A线程执行计数器、寄存器内容
2. 加载B线程
3. 保存B线程执行计数器、寄存器内容
4. 加载A线程

上下文切换过程中的信息，存储在PCB(process control block), 也称 switch frame,保存于CPU中，直到再次被使用

一般触发原因：
1. task timeover
2. io
3. multi task acquire
4. 用户代码让出cpu
5. 硬件中断


## 线程复用
替换 run 块，避免thread频繁创建

## 线程池

组件
1. 线程池管理器：用于创建并管理线程池
2. 工作线程：线程池中的线程
3. 任务接口：每个任务必须实现的接口，用于工作线程调度其运行
4. 任务队列：用于存放待处理的任务，提供一种缓冲机制

线程池核心参数：
1. corePoolSize：指定了线程池中的线程数量。
2. maximumPoolSize：指定了线程池中的最大线程数量。
3. keepAliveTime：当前线程池数量超过 corePoolSize 时，多余的空闲线程的存活时间，即多少时间内会被销毁。
4. unit：keepAliveTime 的单位。
5. workQueue：任务队列，被提交但尚未被执行的任务。
6. threadFactory：线程工厂，用于创建线程，一般用默认的即可。
7. handler：拒绝策略，当任务太多来不及处理，如何拒绝任务。


拒绝策略：
JDK 内置的拒绝策略如下：
1. AbortPolicy ： 直接抛出异常，阻止系统正常运行。
2. CallerRunsPolicy ： 只要线程池未关闭，该策略直接在调用者线程中，运行当前被丢弃的
任务。显然这样做不会真的丢弃任务，但是，任务提交线程的性能极有可能会急剧下降。
3. DiscardOldestPolicy ： 丢弃最老的一个请求，也就是即将被执行的一个任务，并尝试再
次提交当前任务。
4. DiscardPolicy ： 该策略默默地丢弃无法处理的任务，不予任何处理。如果允许任务丢
失，这是最好的一种方案。

线程池工作原理：
> 一直提交线程，直到达到队列最大长度，然后不再提交

1. threadCount < corePollSize, new and submit
2. threadCount = corePollSize, push left work queue
3. work queue full
4. threadCount < maxinumPollSize, new and submit
5. threadCount = maxinumPollSize
    Else: threadCount > maxinumPollSize, err
6. threadCount > corePollSize, thread no work time > keepAliveTime & unit, destory thread
7. threadCount = corePollSize, nothing