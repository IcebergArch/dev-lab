# volatile

## 约定
OS层面确保其一致性，不允许copy到cpu cache，统一从内存中读取

Java 语言提供了一种稍弱的同步机制，即 volatile 变量，用来确保将变量的更新操作通知到其他
线程。volatile 变量具备两种特性，volatile 变量不会被缓存在寄存器或者对其他处理器不可见的
地方，因此在读取 volatile 类型的变量时总会返回最新写入的值。

## 注意

volatile 单次读写是原子的，但多次不是

## 线程共享

Java中通过共享内存实现，核心：
- 可见性
- 有序原子性

JMM java memory model 解决可见性、有序性
Lock 解决原子性

Class（线程）-> ClassA.[synchronized]funxxx() -op-> ClassA.feild