# lock

## 分类

| 维度            | 分类示例 / 类型                          | 描述                                         |
| ------------- | ---------------------------------- | ------------------------------------------ |
| **冲突处理策略**    | 悲观锁 / 乐观锁                          | 悲观锁：假设会冲突 → 先加锁阻塞<br>乐观锁：假设不会冲突 → 不加锁，冲突重试 |
| **阻塞行为**      | 阻塞锁 / 非阻塞锁 / 自旋锁                   | 阻塞锁：线程等待锁挂起<br>非阻塞锁：尝试获取锁失败直接返回或自旋         |
| **作用范围 / 粒度** | 方法级 / 对象级 / 读写锁 / 信号量 / 数据库行锁 / 表锁 | 定义锁保护的资源范围和粒度大小                            |
| **实现机制**      | 内核锁 / 用户态自旋 / CAS / 条件变量           | JVM 层、CPU 原子操作或 OS 内核提供                    |


## 常见概念
乐观锁（Optimistic Lock） ：写时考虑并发，记录版本号，避免写错
悲观锁（Pessimistic Lock）：读写都考虑并发，读写都加锁
CAS自旋：读写都考虑并发，但不加锁


## Java中的锁

synchronized 独占悲观锁
1. 作用于方法时，锁住的是对象的实例(this)；
2. 当作用于静态方法时，锁住的是Class实例，又因为Class的相关数据存储在永久带PermGen
（jdk1.8 则是 metaspace），永久带是全局共享的，因此静态方法锁相当于类的一个全局锁，
会锁所有调用该方法的线程；
3. synchronized 作用于一个对象实例时，锁住的是所有以该对象为锁的代码块。它有多个队列，
当多个线程一起访问某个对象监视器的时候，对象监视器会将这些线程存储在不同的容器中。


Synchronized 核心组件
1) Wait Set：哪些调用 wait 方法被阻塞的线程被放置在这里；
2) Contention List：竞争队列，所有请求锁的线程首先被放在这个竞争队列中；
3) Entry List：Contention List 中那些有资格成为候选资源的线程被移动到 Entry List 中；
4) OnDeck：任意时刻，最多只有一个线程正在竞争锁资源，该线程被成为 OnDeck；
5) Owner：当前已经获取到所资源的线程被称为 Owner；
6) !Owner：当前释放锁的线程。

reentrantlock
具有synchronized功能，同时提供了诸如可响应中断锁、可轮询锁请求、定时锁等
避免多线程死锁的方法。
需手动释放锁

## 其他内容
synchronized底层实现（HotSpot JVM）：

每个Java对象头（Header）包含Mark Word，在64位虚拟机中通常为8字节（开启指针压缩后可能为4字节），其中低3位存储锁状态（001=无锁态，000=轻量级锁，010=重量级锁，101=偏向锁）。

偏向锁：在无竞争场景下，将线程ID直接写入Mark Word，避免CAS开销；当发生竞争时触发偏向撤销（revoke），需全局安全点（safepoint）暂停所有线程，批量将对象恢复为无锁态或升级为轻量级锁。

轻量级锁：线程在栈帧中创建Lock Record（锁记录），通过CAS将Mark Word替换为指向该记录的指针；若失败则自旋尝试（默认10次，由-XX:PreBlockSpin控制），失败后膨胀为重量级锁。

重量级锁：Mark Word保存指向ObjectMonitor的指针；ObjectMonitor结构包含_owner（持有线程）、_waitSet（调用wait()的线程链表）、_entryList（等待进入临界区的线程队列）、_cxq（竞争队列，LIFO插入，FIFO迁移至_entryList）、_count（计数器）等字段；线程park/unpark由JVM委托OS线程调度器完成（如Linux下futex系统调用）。
AQS核心原理（ReentrantLock基础）：

AQS是一个FIFO双向同步队列管理框架，核心是volatile int state（表示同步状态，如ReentrantLock中代表重入次数）、Node节点（含thread、waitStatus、prev、next、nextWaiter）、以及CLH变体队列（Craig, Landin, and Hagersten queue）。

acquire流程：tryAcquire尝试获取锁（非公平锁：直接CAS修改state；公平锁：先检查队列是否为空且无前驱节点）→ 失败则addWaiter加入同步队列 → acquireQueued自旋+CAS+LockSupport.park阻塞 → 被唤醒后再次tryAcquire。

release流程：tryRelease释放锁（state减1，为0则完全释放）→ unpark队列中head.next节点。
ConditionObject基于AQS的单向等待队列，await()将当前线程构造成Node加入condition队列，并释放锁；signal()将condition队列首节点移至AQS同步队列尾部，使其参与锁竞争。

ReentrantReadWriteLock（RRWL）：    
state高16位表示读锁计数（支持共享），低16位表示写锁重入次数（独占）。
写锁获取需满足：读锁数==0 && 写锁数==0 || 当前线程已持写锁。
读锁获取需满足：无写锁占用，或当前线程已持写锁（读写锁可重入）。
存在“写饥饿”问题（持续读请求使写线程长期等待），可通过设置writerShouldBlock()策略缓解（如公平模式下优先让写线程入队）。

StampedLock（JDK 8引入）：
支持三种模式：writeLock（悲观独占）、readLock（悲观共享）、tryOptimisticRead（乐观读）。
乐观读不加锁，仅获取一个long类型的stamp（本质是state的快照值），后续validate(stamp)判断期间是否有写操作（即state是否被修改过）；若未修改，则无需重试，否则降级为readLock。
不支持重入、不支持Condition、不与synchronized互斥（即StampedLock保护的临界区仍可能被synchronized代码干扰），适用于读远多于写的高性能场景。
无锁编程基石：CAS（Compare-And-Swap）与Unsafe/VarHandle

AtomicInteger底层调用Unsafe.compareAndSwapInt(this, valueOffset, expect, update)，对应CPU指令（x86为cmpxchg）。

ABA问题：值由A→B→A，CAS误判成功；解决方式：AtomicStampedReference（增加版本戳）或AtomicMarkableReference（布尔标记）。

Java 9+推荐使用VarHandle替代Unsafe，提供类型安全、内存模型语义明确的原子操作（如VarHandle.compareAndSet、getAndAdd等）。