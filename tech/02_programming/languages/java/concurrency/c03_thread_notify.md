# 线程同步工具

## Semaphore
并发阈值

设置，信号量=0阻塞，归还后再分配
Semaphore 基本能完成 ReentrantLock 的所有工作

## CyclicBarrier
同时起跑

## CountDownLatch
1等多

## AtomicXXX
AtomicReference<V>将一个对象的所
有操作转化成原子操作。

## ReadWriteLock
可单独对某一场景加锁


## 锁分类
可重入锁
公平&非公平
共享&独占


重量级锁（synchronized, 底层 mutex lock）
轻量级锁
偏向锁（消除重入的CAS消耗）
分段锁
锁优化（减少时间、缩小粒度）
锁分离（ReadWriteLock）
锁粗化
锁消除（编译器，即时编译器对于不可能被共享的对象，消除对象加锁）

## 线程基本方法
wait
notify
notifyAll
sleep
join
yield

## 线程交互

join:  主等子
notify: 唤醒等待线程


