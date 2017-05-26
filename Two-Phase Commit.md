# Two-Phase Commit

数据一致性指：在分布式环境下如何让一组节点达成共识，共识的内容包括：变量的值、行为或者决定的过程。一致性可以使分布式系统看上去像一个整体。

一致性通常可以用来：
1. 决定是否在数据库执行一个事务
2. 同步时钟的当前时间
3. 执行分布式算法的下一个阶段（replicated state machine）
4. leader选举

## 一致性定义
一个一致性协议是正确的，当且仅当：
1. Agreement：所有N个节点共同决定同一个变量的值
2. Validity：变量的值一定是由N个节点中的某些节点提议的
3. Termination：所有节点最终会决定变量的值

## Two-Phase Commit
2PC是一种最简单最直接的一致性协议，包括两个阶段：
1. 第一阶段：向每个参与者发送提议，并且收集回复
2. 第二阶段：把投票的结果发送给参与者，并且决定继续执行或者放弃

![](http://the-paper-trail.org/blog/wp-content/uploads/2010/01/tpc-fault-free-phase-1.png)

![](http://the-paper-trail.org/blog/wp-content/uploads/2010/01/tpc-fault-free-phase-2.png)

![](http://blog.chinaunix.net/attachment/201207/15/27105712_1342359032LzmK.png)

![](http://blog.chinaunix.net/attachment/201207/15/27105712_1342358417Cwr1.jpg)

## Coordinator失败
2PC过程中，Coordinator可能会失败，包括：
- 第一阶段
  - A：在发出提议前，coordinator挂了
  - B：部分节点收到了提议后，coordinator挂了
- 第二阶段
  - C：所有节点都收到投票结果前，coordinator挂了

### A
2PC过程还没开始，不影响系统内部的任何状态，但是影响系统的可用性，可以通过Coordinator的HA来提供系统可用性。

### B
部分节点收到提议然后处于等待投票结果状态，同时部分节点没有收到提议，如果coordinator长时间不恢复，收到提议的节点将会一直等待投票结果，可能永远不会恢复。也就是说新的提议将会被卡主，因为之前未完成的提议可能会使节点对某些资源加锁。

### B的解法
当coordinator挂了的时候，Watchdog节点将会接管coordinator的工作：
1. 参与者需要保存提议信息，直到直到所有参与者达成一致
2. Watchdog节点首先从各个参与者那获得所有未完成的提议信息，重新构建coordinator的状态
3. Watchdog实行coordinator的工作，继续未完成的提议
4. 参与者将投票信息发送到Watchdog

![](http://the-paper-trail.org/blog/wp-content/uploads/2010/01/tpc-coordinator-fails-phase-1.png)

但是如果在Watchdog在成功构建coordinator状态前，某个参与者挂了，将会导致Watchdog无法正确构建coordinator状态，而无法保证一致性。

### C
那些没有收到投票结果的参与者，将会一直等待coordinator的结果。

### C的解法
类似于B的解法，在coordinator挂掉的时候，Watchdog可以从参与者那获得当前提议的信息，并重新构建coordinator状态，最后代替coordinator继续执行协议。

![](http://the-paper-trail.org/blog/wp-content/uploads/2010/01/tpc-coordinator-fails-phase-2.png)

## 总结
2PC是一致性协议里面最简单的协议，因为它的简单，很多系统都选择使用2PC协议。2PC协议最大的问题是coordinator挂掉导致可用性的下降。

## Reference
- [Two-Phase Commit](http://the-paper-trail.org/blog/consensus-protocols-two-phase-commit/)
