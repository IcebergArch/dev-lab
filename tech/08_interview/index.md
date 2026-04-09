
---

# 一、Java 锁体系问题清单（text版）

## 基础锁

1. Java 中有哪些锁？如何分类？
2. synchronized 和 Lock 的区别是什么？
3. synchronized 的底层实现原理是什么？
4. synchronized 是如何实现可重入的？
5. synchronized 为什么是线程安全的？
6. synchronized 锁的是对象还是代码？
7. synchronized 方法和 synchronized 块有什么区别？
8. synchronized 为什么效率比早期版本高？

---

## 锁升级

9. synchronized 的锁升级过程是什么？
10. 什么是偏向锁？
11. 什么是轻量级锁？
12. 什么是重量级锁？
13. 偏向锁适用于什么场景？
14. 轻量级锁失败后会发生什么？
15. 为什么要进行锁升级？

---

## ReentrantLock

16. 什么是 ReentrantLock？
17. ReentrantLock 为什么是可重入锁？
18. ReentrantLock 和 synchronized 有什么区别？
19. ReentrantLock 为什么需要手动释放锁？
20. tryLock() 有什么作用？
21. ReentrantLock 如何实现公平锁？
22. ReentrantLock 默认是公平锁还是非公平锁？

---

## 公平锁 / 非公平锁

23. 什么是公平锁？
24. 什么是非公平锁？
25. 公平锁和非公平锁的区别是什么？
26. 为什么非公平锁吞吐量更高？
27. hasQueuedPredecessors() 的作用是什么？
28. 非公平锁为什么允许插队？

---

## 读写锁

29. 什么是读写锁？
30. 为什么读写锁适用于读多写少场景？
31. 读写锁如何实现读读共享？
32. 写锁为什么必须互斥？
33. 什么是锁降级？

---

## AQS

34. 什么是 AQS？
35. AQS 的核心思想是什么？
36. state 在 AQS 中代表什么？
37. AQS 为什么使用 CLH 队列？
38. AQS 中 Node 的作用是什么？
39. AQS 获取锁的流程是什么？
40. AQS 释放锁的流程是什么？
41. park() 和 unpark() 的作用是什么？
42. 为什么 AQS 要使用 CAS？

---

## Condition

43. 什么是 Condition？
44. Condition 和 wait/notify 的区别是什么？
45. Condition await() 做了什么？
46. Condition signal() 做了什么？
47. Condition 为什么需要两个队列？

---

# 二、JVM & GC 问题清单（text版）

## JVM 内存模型

48. JVM 内存结构包括哪些部分？
49. 堆内存如何划分？
50. 什么是新生代？
51. 什么是老年代？
52. 什么是 Eden 区？
53. 什么是 Survivor 区？

---

## GC 基础

54. 什么是 GC？
55. Minor GC 和 Full GC 的区别是什么？
56. 什么情况下会触发 Minor GC？
57. 什么情况下会触发 Full GC？
58. 什么是 Stop-The-World？

---

## 垃圾回收算法

59. 常见垃圾回收算法有哪些？
60. 什么是标记-清除算法？
61. 什么是标记-整理算法？
62. 什么是复制算法？
63. 三色标记法是什么？

---

## GC 收集器

64. Serial GC 的特点是什么？
65. Parallel GC 的特点是什么？
66. CMS GC 的特点是什么？
67. G1 GC 的特点是什么？
68. ZGC 的特点是什么？

---

## GC 调优

69. 如何判断 GC 是否有问题？
70. 如何查看 GC 日志？
71. 如何定位 Full GC 频繁问题？
72. Minor GC 频繁如何优化？
73. 如何定位内存泄漏？
74. 如何分析 heap dump？
75. 如何减少 GC 停顿时间？

---

## 内存泄漏

76. 什么是内存泄漏？
77. ThreadLocal 为什么可能导致内存泄漏？
78. 静态集合为什么容易导致泄漏？
79. 如何通过 MAT 定位泄漏对象？

---

# 三、并发工具类问题清单（text版）

80. 什么是 Semaphore？
81. Semaphore 的使用场景是什么？
82. 什么是 CountDownLatch？
83. CountDownLatch 的工作原理是什么？
84. 什么是 CyclicBarrier？
85. CyclicBarrier 和 CountDownLatch 的区别是什么？
86. 什么是 StampedLock？
87. StampedLock 为什么性能更高？

---

# 四、RAG 检索问题清单（text版）

这是 AI 系统设计重点。

---

## RAG 基础

88. 什么是 RAG？
89. RAG 的核心流程是什么？
90. 为什么需要 RAG？

---

## Chunk

91. 什么是 Chunk？
92. 为什么 Chunk 很重要？
93. Chunk size 如何选择？
94. overlap 的作用是什么？
95. 什么是语义切块？

---

## Embedding

96. 什么是 Embedding？
97. Embedding 为什么能表示语义？
98. 向量维度有什么影响？

---

## 向量检索

99. 什么是向量检索？
100. 什么是 ANN？
101. HNSW 的基本原理是什么？
102. IVF 的基本原理是什么？
103. 向量相似度如何计算？
104. 什么是 Cosine 相似度？

---

## Rerank

105. 什么是 Rerank？
106. 为什么需要 Rerank？
107. Rerank 和 Embedding 有什么区别？

---

## Hybrid 检索

108. 什么是 Hybrid Search？
109. 为什么要结合 BM25？
110. 多路召回的意义是什么？

---

# 五、系统设计级问题（高级）

这部分适合高级工程师。

---

## 高并发

111. 如何设计一个高并发系统？
112. 如何避免锁竞争？
113. 如何减少 GC 对系统的影响？
114. 如何设计无锁结构？

---

## 性能优化

115. 如何分析系统性能瓶颈？
116. CPU 高使用率如何排查？
117. 内存高使用率如何排查？
118. RT 抖动如何定位？

---

## RAG 系统设计

119. 如何设计一个高性能 RAG 系统？
120. 如何优化 RAG 检索延迟？
121. 如何减少 hallucination？
122. 如何实现多轮对话记忆？

---

# 六、如果你用于面试，我建议这样使用

这份问题可以：

```text
第一遍：逐题口述答案
第二遍：写出关键流程
第三遍：补源码细节
第四遍：模拟真实面试
```

效果会非常明显。

---

# 七、如果你的目标是高级 Java

我建议重点优先级：

```text
★★★★★
AQS
ReentrantLock
GC 调优
JVM 内存模型

★★★★
ReadWriteLock
Condition
并发工具类

★★★
RAG（如果涉及 AI）
```

---

# 如果你想继续优化这份问题集

我可以帮你做下面这些升级版本：

1. 每题附 **标准答案要点版**
2. 每题附 **源码路径**
3. 每题附 **面试评分标准**
4. 生成 **可导入笔记系统（Markdown）版本**
5. 生成 **模拟面试题随机抽题系统**

这些会非常适合长期复习使用。