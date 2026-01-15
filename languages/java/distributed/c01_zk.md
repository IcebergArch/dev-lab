# zookeeper

Zookeeper 集群是一个基于主从复制的高可用集群，每个服务器承担如下三种角色中的一种
Leader
Follower
Observer


## zk选举

在 ZAB ( ZooKeeper Atomic Broadcast , ZooKeeper 原子消息广播协议） 协议的事务编号 Zxid 

设计中，Zxid 是一个 64 位的数字，其中低 32 位是一个简单的单调递增的计数器

### epoch

> 避免宕机恢复的机器扰乱现有系统

epoch：可以理解为当前集群所处的年代或者周期，每个 leader 就像皇帝，都有自己的年号，所以每次改朝换代，leader 变更之后，都会在前一个年代的基础上加 1。


### zab

Zab 协议有两种模式，它们分别是恢复模式（选主）和广播模式（同步）。

当服务启动或者在领导者崩溃后，Zab 就进入了恢复模式，当领导者被选举出来，且大多数 Server完成了和 leader 的状态同步以后，恢复模式就结束了。

状态同步保证了 leader 和 Server 具有相同的系统状态。


### ZAB 协议 4 阶段

> Leader election（选举阶段-选出准 Leader）
1. Leader election（选举阶段）：
节点在一开始都处于选举阶段，只要有一个节点得到超半数节点的票数，它就可以当选准leader。
- 只有到达 广播阶段（broadcast） 准 leader 才会成为真正的 leader。这一阶段的目的是就是为了选出一个准 leader，然后进入下一个阶段。

> Discovery（发现阶段-接受ᨀ议、生成 epoch、接受 epoch）
2. Discovery（发现阶段）：
在这个阶段，followers 跟准 leader 进行通信，同步 followers 最近接收的事务提议。
- 这个一阶段的主要目的是发现当前大多数节点接收的最新提议，并且准 leader 生成新的 epoch，让 followers 接受，更新它们的 accepted Epoch一个 follower 只会连接一个 leader，如果有一个节点 f 认为另一个 follower p 是leader，f 在尝试连接 p 时会被拒绝，f 被拒绝之后，就会进入重新选举阶段。

> Synchronization（同步阶段-同步 follower 副本）
3. Synchronization（同步阶段）：同步阶段主要是利用 leader 前一阶段获得的最新提议历史，同步集群中所有的副本。
- 只有当 大多数节点都同步完成，准 leader 才会成为真正的 leader。follower 只会接收 zxid 比自己的 lastZxid 大的提议。

> Broadcast（广播阶段-leader 消息广播）
4. Broadcast（广播阶段）：到了这个阶段，Zookeeper 集群才能正式对外提供事务服务，并且 leader 可以进行消息广播。同时如果有新的节点加入，还需要对新节点进行同步。

ZAB 提交事务并不像 2PC 一样需要全部 follower 都 ACK，只需要得到超过半数的节点的 ACK 就
可以了。

#### 投票机制

初始化
- 所有节点启动或 Leader 崩溃时进入 LOOKING 状态
- 默认投票给自己

投票轮次
- 每个节点向集群广播自己当前投票（候选 Leader）
- 节点收到其他节点投票后：
    - 比较 zxid
    - 若 zxid 或 serverId 更优，则更新自己的投票

投票收敛
- 投票不断传播、更新
- 当某个节点的票数获得多数认可时： 
   - 该节点成为 Leader
   -  其他节点变为 Follower

Leader 确认
- Leader 启动并同步最新事务数据
- Follower 开始向 Leader 转发写请求

实现中 zxid 是一个 64 为的数字，它高 32 位是 epoch 用来标识 leader 关系是否改变，每次一个 leader 被选出来，它都会有一个新的 epoch。低 32 位是个递增计数。