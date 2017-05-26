# Failure Modes in Distributed System
在分布式环境中，任何节点都有可能因为各种原因导致调用失败，如何处理失败是分布式系统的关键，而在处理失败前需要先对失败进行定义和分类，本文将讨论不同的失败模型。

## 不同种类的Failure Modes
Failure Modes可以分为如下6种：

### Byzantine failures
- 发送不同的事实：服务器可以向A发送m=true的事实，同时向B发送m=false的事实；
- 伪造事实：服务器从A收到m=true的事实，但是可以向B发送：A给我发送了m=false的事实。

### Authentification detectable byzantine failures
类似于Byzantine failure，可以发送不同的实时，但是不能伪造事实。

### Performance failures
服务器能响应客户端的请求，并返回正确的值，但是请求会返回太快或太慢，常见情况：服务器由于负载过高，导致响应超时。

### Omission failures
Omission failure是Performance failure的一种特殊情况，即服务器响应无限期延迟。

### Crash failures
服务器停止响应。

### Fail-stop failures
当服务器停止响应的时候，可以假设其他正常服务器可以检测到处于无法响应状态的服务器。

## Failure Modes之间的关系
不同的Failure Mode意味着我们可以对不同的服务进行不同的假设,对于Fail-Stop failure，我们可知道服务失败了。

不同的Failure Mode之间存在包含关系，如果一个服务会出现Performance failure，那么必定也会出现Omission failure。之间的包含关系如下：Byzantine failures ⊃ authentification detectable byzantine failures ⊃ performance failures ⊃ omission failures ⊃ crash failures ⊃ fail-stop failures

## Failure Semantics
如何定义一个服务的Failure Mode？

> Failure semantic: a server exhibits a given failure semantic if the probability of failure modes which are not covered by the failure semantic is sufficiently low

如果一个服务的Failure Mode是Crash failure，那么出现Byzantine failure的概率是非常小的。也就是说调用该服务的时候，只需要考虑Crash failure就可以了。

## Reference
- [FAILURE MODES IN DISTRIBUTED SYSTEMS](http://alvaro-videla.com/2013/12/failure-modes-in-distributed-systems.html)
