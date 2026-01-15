# ThreadLocal

Thread.ThreadLocalMap.put(XXX)

ThreadLocal指向自己
- Thread -> Thread Obj -> OS.TLS(thread local Storage)
- JVM set curr thread.TLS -> Thread Obj

Thread.currentThread() 本质是在读取当前线程的 TSL 里的指针，自然获取的就是当前对象