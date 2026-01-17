# AQS 
AbstractQueuedSynchronizer
AQS 定义了一套多线程访问共享资源的同步器框架，许多同步类实现都依赖于它，如常用的ReentrantLock/Semaphore/CountDownLatch。

AQS 定义两种资源共享方式
Exclusive 独占资源-ReentrantLock
Exclusive（独占，只有一个线程能执行，如 ReentrantLock）
Share 共享资源-Semaphore/CountDownLatch
Share（共享，多个线程可同时执行，如 Semaphore/CountDownLatch）。