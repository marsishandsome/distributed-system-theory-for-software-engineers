# Chandy-Lamport Snapshotting
Chandy-Lamport算法是为了在分布式系统中记录全局状态快照的问题。

## Snapshots
为什么需要Snapshots？
- Checkpointing：任务失败的时候重启恢复状态
- Collecting garbage：回收没有引用的对象
- Detecting deadlocks：检查应用当前的状态
- Other debugging：比printf方便些

状态包括
- 进程的状态
- 进程间正在发送的消息

状态发送变化的事件
- 进程发送消息
- 进程收到消息
- 进程运行导致状态发送变化

## Chandy-Lamport算法
### 要求
- 做Snapshot的时候不能影响进程正常运行（不停止发送消息，不停止进程）
- 每个进程能记录自己的状态
- 分布式收集状态
- 任意一个进程可以发起Snapshot操作

### 初始化Snapshot
1. 进程P(i)初始化Snapshot操作
2. P(i)记录自己的状态，同时准备一个特殊的marker消息
3. 把marker消息发送给其他n-1个进程
4. 开始记录所有从C(j, i)收到的消息(j != i)

### 传播Snapshot
对于所有的进程P(j)(包括初始化的进程)，当从C(k, j)收到一条消息时
1. 如果是marker消息，并且是第一次收到
  - P(j)记录自己的状态
  - 标记C(k, j)为收到过marker消息
  - 把marker消息发送给其他n-1个进程
2. 如果是marker消息，但不是第一次收到
  - 标记C(k, j)为收到过marker消息
3. 如果是普通消息，并且C(l,j)没有收到过marker消息，则记录该消息
4. 如果是普通消息，但是C(l,j)收到过marker消息，则忽略该消息

### 结束Snapshot
1. 首先所有进程都收到过一个marker消息，并记录了自己进程的状态
2. 然后所有进程都收到了n-1个marker消息，并记录所有channel的状态
3. 最后一个中央服务器可以收集这些信息，并组装成全局的Snapshot

## References
- [Distributed Snapshots: Determining Global
States of Distributed Systems](https://www.microsoft.com/en-us/research/wp-content/uploads/2016/12/Determining-Global-States-of-a-Distributed-System.pdf)
- [Chandy-Lamport Snapshotting ](https://www.cs.princeton.edu/courses/archive/fall16/cos418/docs/P8-chandy-lamport.pdf)
- [Chandy-Lamport algorithm wikipedia](https://en.wikipedia.org/wiki/Chandy-Lamport_algorithm)
