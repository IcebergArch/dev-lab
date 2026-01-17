# concurrency

## Java线程

extends Thread => thread.start()
implements Runnable => thread.run()

## Java线程池

executor

single: exception, then build new one go on
fixed: queue and run
cached: delete overtime old thread, new one put in executors
scheduled: clock, and run

## 线程状态

new -> runnable -> running -> bolcked -> waiting -> TERMINATED

## 操作线程结束

run() call() 执行结束
exception 

stop => 死锁风险


## sleep & wait

thead.sleep() 让出cpu
object.wait() 放弃对象锁，重新排队

## start & run

start 异步，提交线程内容并执行，由JVM起新线程执行并交由OS调度
run 同步，为线程执行体，可以与不同的执行器组合