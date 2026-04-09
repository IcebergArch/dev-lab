## Agent

## 架构

user -> Agent Group -> LLM

user -> Agent Group:
    Memory System
        1) Short Memory: 最近 N 条消息
        2) Episodic Memory: 摘要后的历史
        3) Semantic Memory: 向量知识库
        4) Cache: Q-A 快速命中
    Cache 类型：
        1) Exact Cache：完全相同问题
        2) Semantic Cache：相似问题
        3) Tool Result Cache：工具执行缓存

Agent Group -> LLM:
    1) system & user & accessor
    2) zero-shot | flew-shot
    3) prompt(chain-of-shot, Human-in-the-loop, role-task-action-limit)

Agent Group:
    1) get response from llm
    2) parse llm response
        a. call util
        b. run script
    3) display to user

## Memory 优化

1. Short Memory
    问题：每次调用都带上太多最近消息 → 请求过大、延迟高、Token 超限
    策略：
    保留最近 N 条消息（如 10~20 条）
    对文本做 摘要或抽象化存储（保留语义核心）
    对可忽略的历史对话过滤掉
2. Long / Semantic Memory
    问题：全量历史对 LLM 输入成本高
    策略：
    使用 向量检索 + embedding
    结合 相似度阈值取最相关内容
    对历史知识定期做 chunk / summarize / prune
3. Cache
    问题：重复问题每次都调用 LLM，成本高
    策略：
    Exact Match Q-A cache
    Semantic Match Q-A cache（相似问题）
    工具执行结果缓存（Tool Cache）
    配合 TTL（过期策略）和 LFU/LRU

## Prompt优化

1. Prompt 长度控制
    策略：
    使用 动态 memory 注入而不是全量历史
    对重复信息使用 summarization
    将系统、工具、例子分块管理
2. Few-shot / Chain-of-shot 优化
    策略：
    例子选择 最相关的上下文而非全部例子
    可动态切换 chain-of-shot，减少不必要步骤
    Human-in-the-loop 仅对低置信度情况触发
3. Prompt 调优
    策略：
    对 LLM 输出加 role-task-action-limit 控制
    对多步骤任务，可拆成 多轮 Prompt → ReAct Loop

## LLM调用优化
1. Token / Cost 控制
    策略：
    限制模型输出长度
    使用较小模型处理低复杂度请求
    对高频任务使用 fine-tuned 模型或 embedding 模型 替代
2. 并发与批量
    策略：
    将多用户请求 批量或异步调用
    使用 streaming 输出提升用户体验
3. ReAct / 多步推理优化
    策略：
    仅在必要时循环调用 LLM，避免无限循环
    每轮加入 tool 执行结果更新 memory

## RAG缓存优化（save-match-update）

{ query, answer, embedding }

### 一、查询端优化（Query）

1. **Query 标准化**
   - 去除多余空格、标点  
   - 统一大小写、同义词替换  
   - 避免同义表达导致 Cache miss  

2. **Query Embedding**
   - 将输入做向量化，用语义相似度匹配  
   - 相似问题也能命中 Cache  

3. **Query 分类**
   - 根据任务类型或场景打标签  
   - 限制匹配范围，提高命中率  

---

### 二、存储端优化（Cache）

1. **Cache Key 多维度设计**
   - 用户ID/会话ID + query hash/embedding + task type  
   - 避免不同上下文干扰，提高复用率  

2. **Embedding 存储**
   - query/response 做向量存储  
   - 支持 semantic match / ANN 检索  

3. **Cache 内容丰富化**
   - 存 LLM 输出 + memory context + tool 结果  
   - 避免重复检索，提高复用率  

---

### 三、匹配策略优化

1. **Exact Match + Semantic Match**
   - 优先 exact match  
   - miss 后用 semantic match  

2. **Top-K 相似命中**
   - 返回 top-k cache item  
   - 可合并结果或选最相关  

3. **动态阈值**
   - 高频 query 严格匹配  
   - 低频 query 宽松匹配  

---

### 四、更新策略优化

1. **智能缓存写入**
   - 仅可信 LLM 输出写入 Cache  
   - 高价值 query 优先保留  

2. **摘要 + 标准化**
   - 对输出进行摘要、格式化再缓存  
   - 避免微差异导致命中失败  

3. **Cache 清理策略**
   - 使用 LFU/LRU 淘汰策略  
   - 保持热门 query 热区，提高命中率  

---
