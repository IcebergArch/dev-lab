# Web应用

## 拦截器

Filter：拦请求（HTTP层）
Interceptor：拦接口（Controller层）
AOP：拦方法（代码层）

## AOP

面向切面编程也是面向对象编程的扩展.从而使得业务逻辑各部分之间的耦合度降低，提高程序的可重用性，同时提高了开发的效率。

1.AOP底层代用的JDK动态代理模式，使用它的前提是必须得有接口。
如果没有接口只有实现类的情况下，可以采用cglib动态代理。

## 循环引用

| 缓存                             | 作用               | 存储内容           |
| ------------------------------ | ---------------- | -------------- |
| **一级缓存** singletonObjects      | 正常的单例 Bean       | 完整初始化好的对象      |
| **二级缓存** earlySingletonObjects | 早期曝光的对象          | 初始化未完成的对象引用    |
| **三级缓存** singletonFactories    | ObjectFactory 工厂 | 用于创建早期 Bean 引用 |


new出来 -> 放三级缓存（ObjectFactory）
属性注入阶段 -> 放二级缓存（早期 Bean 引用）
初始化完成 -> 放一级缓存（完整 Bean）

⚠️ 注意：三级缓存 不是直接放对象，是放 ObjectFactory，延迟生成 Bean 引用
二级缓存才真正放“半成品对象引用”


singletonObjects -> beanName <> instaance
earlySingletonObjects -> 初次实例化完 new instance
singletonFactories -> 三级缓存工厂 factoryBean

只用一级缓存会导致，属性设置完成的Bean和未设置完成的Bean都保留在一级缓存中，可能导致空指针情况
只用两级缓存会导致，循环引用填充的对象可能并不是代理过的对象


## ThreadLocal

threadLocal.get() 
   ↓
获取当前线程 Thread.currentThread()
   ↓
访问线程的 threadLocals Map
   ↓
用 ThreadLocal（this）作为 key 查找 value
   ↓
返回线程独立的值

怎么避免内存泄漏
每次使用完ThreadLocal，都调用它的remove()方法，清除数据。


# Question & Answer

读写锁，https://blog.csdn.net/small_love/article/details/111406016

Redis为什么快，https://cloud.tencent.com/developer/article/1691655

Redis为什么快，https://draveness.me/whys-the-design-redis-single-thread/

为何弃用stop, suspend, resume, https://blog.csdn.net/xingjiarong/article/details/47984659

sync & ReenXXXLock, https://www.cnblogs.com/upnote/p/12984626.html

lockSupport, https://www.jianshu.com/p/f1f2cd289205

Java 锁， https://www.cnblogs.com/upnote/p/12984626.html

https://www.ibm.com/developerworks/java/library/j-jtp10264/index.html?ca=drs-

线程安全的集合， https://corsairmaster.github.io/2018/11/22/%E5%A6%82%E4%BD%95%E8%8E%B7%E5%BE%97%E7%BA%BF%E7%A8%8B%E5%AE%89%E5%85%A8%E7%9A%84%E9%9B%86%E5%90%88/

UDP为什么要可靠，https://www.infoq.cn/article/how-to-make-udp-reliable

HTTP状态码，https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Status/304

http http://47.98.159.95/my_blog/blogs/net/http/013.html#s-maxage