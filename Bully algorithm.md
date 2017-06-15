# Bully algorithm (霸道选举算法)
霸道选举算法是一种分布式选举算法，每次都会选出存活的进程中ID最大的候选者。

## 霸道选举算法的选举流程
选举过程中会发送以下三种消息类型：
1. Election消息：表示发起一次选举
2. Answer(Alive)消息：对发起选举消息的应答
3. Coordinator(Victory)消息：选举胜利者向参与者发送选举成功消息

触发选举流程的事件包括：
1. 当进程P从错误中恢复
2. 检测到Leader失败

选举流程：
1. 如果P是最大的ID，直接向所有人发送Victory消息，成功新的Leader；否则向所有比他大的ID的进程发送Election消息
2. 如果P再发送Election消息后没有收到Alive消息，则P向所有人发送Victory消息，成功新的Leader
3. 如果P收到了从比自己ID还要大的进程发来的Alive消息，P停止发送任何消息，等待Victory消息（如果过了一段时间没有等到Victory消息，重新开始选举流程）
4. 如果P收到了比自己ID小的进程发来的Election消息，回复一个Alive消息，然后重新开始选举流程
5. 如果P收到Victory消息，把发送者当做Leader

## 霸道选举算法的假设
霸道选举算法的假设包括：
- 假设了可靠的通道通信，更进一步的假设是系统中任何两个进程之间都可以通信。
- 每个进程都知道其他进程的编号，也就是说算法依赖一个全局的数据。
- 假设进程能够明确地判断出一个正常运行的进程和一个已经崩溃的进程。

## References
- [Bully algorithm wikipedia](https://en.wikipedia.org/wiki/Bully_algorithm)
