# 阻塞队列

producer -> queue -> consume

queue empty,block consume

queue full, block producer


## 常用方法
![队列常用方法](../images/queue_op_fun.png)

## JAVA中对对应的工具
1. ArrayBlockingQueue ：由数组结构组成的有界阻塞队列。
2. LinkedBlockingQueue ：由链表结构组成的有界阻塞队列。 
3. PriorityBlockingQueue ：支持优先级排序的无界阻塞队列。
4. DelayQueue：使用优先级队列实现的无界阻塞队列。
5. SynchronousQueue：不存储元素的阻塞队列。
6. LinkedTransferQueue：由链表结构组成的无界阻塞队列。
7. LinkedBlockingDeque：由链表结构组成的双向阻塞队列

公平&非公平：

ArrayBlockingQueue fairQueue = new ArrayBlockingQueue(1000,true);


LinkedBlockingQueue

（两个独立锁提高并发）：对于生产者端和消费者端分别采用了独立的锁来控制数据同步


PriorityBlockingQueue

（compareTo 排序实现优先）


DelayQueue

（缓存失效、定时任务 ）


SynchronousQueue

（不存储数据、可用于传递数据）
是一个不存储元素的阻塞队列。每一个 put 操作必须等待一个 take 操作，否则不能继续添加元素。


LinkedTransferQueue：
1. transfer 方法：如果当前有消费者正在等待接收元素（消费者使用 take()方法或带时间限制的poll()方法时），transfer 方法可以把生产者传入的元素立刻 transfer（传输）给消费者。如果没有消费者在等待接收元素，transfer 方法会将元素存放在队列的 tail 节点，并等到该元素被消费者消费了才返回。
2. tryTransfer 方法。则是用来试探下生产者传入的元素是否能直接传给消费者。如果没有消费者等待接收元素，则返回 false。和 transfer 方法的区别是 tryTransfer方法无论消费者是否接收，方法立即返回。而 transfer 方法是必须等到消费者消费了才返回。


在初始化 LinkedBlockingDeque 时可以设置容量防止其过渡膨胀。另外双向阻塞队列可以运用在“工作窃取”模式中。