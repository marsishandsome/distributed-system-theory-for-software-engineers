# Three-phase Commit

## 2PC的问题
2PC的第二阶段，在coordinator发送投票结果给参与者后，参与者会直接执行事务，而不会去管其他参与者有没有收到执行事务的消息。

如果某个参与者和coordinator同时挂了，同时其他参与者都还没收到投票结果，新的coordinator在恢复状态的时候，不知道该事务要执行还是取消：
1. 如果采用悲观策略直接取消，当挂掉的参与者收到的是执行时，将会导致不一致
2. 如果采用乐观策略直接执行，当挂掉的参与者收到的是取消时，将会导致不一致

所以在这种特殊情况下，2PC只能等待所有挂掉的参与者恢复，而等待参与者会极大的影响系统可用性。

## 3PC
3PC实际上是把2PC的第二阶段拆分成两个步骤，形成三个阶段：
1. canCommit阶段：coordinator向参与者发送事务请求，并等待回复
2. preCommit阶段：coordinator向参与者发送preCommit，并等待回复
3. doCommit阶段：当coordinator收到所有参与者的ACK消息后，发送doCommit，参与者收到doCommit后执行事务

![](https://upload.wikimedia.org/wikipedia/en/3/39/Three-phase_commit_diagram.png)

如果某个参与者和coordinator同时挂了，新的coordinator在恢复状态的时候：
1. 剩下的节点最近的日志都是第一阶段的accept：那么不可能有人进入commit，因为所有节点必须经过prepare-commit，此时挂掉节点可能是第一阶段cancel或者accept或者prepare-commit，可以安全选择abort
2. 至少有一个节点收到prepare-commit了：那么说明participant不可能在第一阶段的应答中就cancel了，即要么是accept,prepare-commit或者commit，而这三种情况下，整个系统都是可以决定提交的
3. 有至少一个返回commit了：那么挂掉的节点只可能是prepare-commit和commit状态，这样整个系统状态也是可以提交的

简单概括一下就是，如果挂掉的那台机器已经执行了commit，那么协调者可以从所有未挂掉的参与者的状态中分析出来，并执行commit。如果挂掉的那个参与者执行了rollback，那么协调者和其他的参与者执行的肯定也是rollback操作。

## 总结
所以，再多引入一个阶段之后，3PC解决了2PC中存在的那种由于协调者和参与者同时挂掉有可能导致的数据一致性问题。

## 3PC存在的问题
在doCommit阶段，如果参与者无法及时接收到来自协调者的doCommit或者rebort请求时，会在等待超时之后，会继续进行事务的提交。

所以，由于网络原因，协调者发送的abort响应没有及时被参与者接收到，那么参与者在等待超时之后执行了commit操作。这样就和其他接到abort命令并执行回滚的参与者之间存在数据不一致的情况。

## Reference
- [Consensus Protocols: Three-phase Commit](http://the-paper-trail.org/blog/consensus-protocols-three-phase-commit/)
- [Three-phase commit protocol](https://en.wikipedia.org/wiki/Three-phase_commit_protocol)
- [Analysis and Verification of Two-Phase Commit & Three-Phase Commit Protocols](http://www.win.tue.nl/~atif/reports/paper4ICET.pdf)
- [深入理解分布式系统的2PC和3PC](http://www.hollischuang.com/archives/1580)
- [Increasing the Resilience of Distributed and Replicated Database Systems](http://webee.technion.ac.il/~idish/Abstracts/jcss.html)
- [三阶段提交协议如何避免协调者状态未知的情况？](https://www.zhihu.com/question/36899436)
