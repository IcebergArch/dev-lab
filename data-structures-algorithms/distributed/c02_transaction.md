# transaction

CAP：Consistency（一致性）、 Availability（可用性）、Partition tolerance（分区容错性）

BASE：是在 CAP 理论的基础之上的延伸。
包括 基本可用（Basically Available）、柔性状态（Soft State）、最终一致性
（Eventual Consistency）。

## 事务算法

- 2-phase commit pre-commit
- 3-phase commit can-pre-do commit
- 柔性事务
    - 两段式
    - 补偿型，TCC（try-confirm-cancel）|| saga, A OK A 提交，B 不OK B 回滚，再补偿一个 A 回滚
    - 异步确保，事务发起者发送消息，MQ消费者决定是否回滚，不回滚就通过MQ继续传递，否则终止，前置节点会check事务状态确认是否回滚
    - 最大努力通知，允许达到最大尝试次数后正常结束事务

## 一致性算法

### Paxos
> 解决的问题是一个分布式系统如何就某个值（决议）达成一致。

Paxos 三种角色：Proposer，Acceptor，Learners
- Proposer：只要 Proposer 发的提案被半数以上 Acceptor 接受，Proposer 就认为该提案里的 value 被选定了。
- Acceptor：
只要 Acceptor 接受了某个提案，Acceptor 就认为该提案里的 value 被选定了。
- Learner：
Acceptor 告诉 Learner 哪个 value 被选定，Learner 就认为那个 value 被选定。


阶段一（准 leader 确定 ）：
1. (a) Proposer 选择一个提案编号 N，然后向半数以上的 Acceptor 发送编号为 N 的 Prepare 请求。
2. (b) 如果一个 Acceptor 收到一个编号为 N 的 Prepare 请求，且 N 大于该 Acceptor 已经响应过的所有 Prepare 请求的编号，那么它就会将它已经接受过的编号最大的提案（如果有的话）作为响应反馈给 Proposer，同时该 Acceptor 承诺不再接受任何编号小于 N 的提案。

阶段二（leader 确认）：
1. (a) 如果 Proposer 收到半数以上 Acceptor 对其发出的编号为 N 的 Prepare 请求的响应，那么它就会发送一个针对[N,V]提案的 Accept 请求给半数以上的 Acceptor。注意：V 就是收到的响应中编号最大的提案的 value，如果响应中不包含任何提案，那么 V 就由 Proposer 自己决定。
2. (b) 如果 Acceptor 收到一个针对编号为 N 的提案的 Accept 请求，只要该 Acceptor 没有对编号大于 N 的 Prepare 请求做出过响应，它就接受该提案。

### zab

### Raft

### NWR

### Gossip

### 一致性hash

