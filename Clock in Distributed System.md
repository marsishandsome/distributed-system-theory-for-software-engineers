# Clock in Distributed System
分布式计算在没有共享时钟的环境下，如何确定不同事件的先后关系呢？本文主要探讨一下分布式环境下的时钟问题。

## Lamport Clock
Lamport Clock是一种逻辑时钟，事件之间的因果关系叫：Happened-before，用->表示。例如a->b，叫做a Happended-before b，即b是由a引起的。

### Happended-before定义
若事件a、b满足以下任意条件，则记作a->b：
1. a、b在同一进程内，并且a发生在b之前
2. a代表某个进程的消息发送事件，b代表另外一个进程对该消息的接收事件

### 传递性
Happens-before关系满足传递性：(a->b && b->c) => a->c。

### 偏序
通过Happens-before关系，可以把系统中的所有事件构建起一个偏序集。

### 时间戳计算算法
每个进程Pi维护一个本地计数器Ci，按照以下的规则更新Ci：
1. 每次执行一个事件之前，Ci加1
2. 当Pi发送消息m给Pj的时候，在消息m上附着上Ci
3. 当接收进程Pj接收到Pi的发送的消息时，更新自己的Cj = max{Cj,Ci} + 1

### 性质
根据算法，每个事件都会生成一个时间戳，事件a的时间戳记作C(a)，满足：
1. a->b => C(a) < C(b)
2. (C(a) < C(b) => a->b) = false

也就是说事件a、b满足Happends-before关系，则a的时间戳一定小于b的时间戳，但是如果a的时间戳小于b的时间戳，那么a、b之间不一定存在Happends-before关系。

### 应用
Lamport clock可以保证存在因果关系事件的时间戳是递增的（不存在因果关系的事件的时间戳是任意的）。一般在日志处理，当需要按照因果关系排序时，会用到。

## Vector Clock
Lamport clock可以保证a->b => C(a) < C(b)，但是不能保证C(a)<C(b) => a->b。Lamport clock的问题是：事件a和事件b实际发生的先后顺序不能仅仅通过比较C(a)和C(b)来决定。Vector Clock就是用来解决这个问题的，即VC(a) < VC(b)可以推出事件a发生在事件b之前。

### 定义
Vector Clock是一个向量，向量的每个分量为(node，count），node即为分布式系统的节点，count为对应节点上的版本，在处理事件前count会对将该值递增，当它需要和其它节点进行同步的时候也会把count带上。通过比较这些向量的大小，来确定事件发生的顺序。

假如一个向量的所有分享量的count值都小于或等于另一个向量，可以认为后者并前者更新，否则存在冲突。

### 小于的定义
VC(a) < VC(b) <=> (VC(a)[i] <= VC(b)[i]，对于任意i) && ()存在j满足VC(a)[j] < VC(b)[j])

### 算法
为每个进程Pi维护一个向量VC，也就是Pi的Vector Clock，这个向量VC有如下属属性：
1. VCi[i]是到目前为止进程Pi上发生的事件的个数
2. VCi[k]是进程Pi知道的进程Pk发生的事件的个数(即Pi对Pj的知识)

每个进程的VC可以通过以下规则进行维护：
1. 进程Pi每次执行一个事件之前，将VCi[i]加1
2. 当Pi发送消息m给Pj的时候，在消息m上附着上VCi(进程Pi的向量时钟)
3. 当接收进程Pj接收到Pi的发送的消息时，更新自己的VCj[k] = max{VCj[k],VCi[k]} ，对于所有的k

#### 例子
![](https://upload.wikimedia.org/wikipedia/commons/thumb/5/55/Vector_Clock.svg/1450px-Vector_Clock.svg.png)

### 性质
根据算法，每个事件都会生成一个Vector，事件a的时间戳记作VC(a)，满足：
1. a->b => VC(a) < VC(b)
2. VC(a) < VC(b) => a->b

也就是说事件a、b满足Happends-before关系，则a的Vector Clock一定小于b，同时如果a的Vector Clock小于b，那么a、b之间一定存在Happends-before关系。

### 应用
通过Vector Clock可以直接判断出两个事件是否有因果关系，这个特性在支持多点写的分布式数据库中会用到，例如Dynamo。

## TrueTime
Spanner提供TrueTime API，你可以认为这个就是一个全局时钟，就是从一台机器上获得的，只不是这个API的返回值不是一个具体的值，而是一个区间，保证真实的wall time在这个区间中。有了这个API加上一些wait机制，就可以给所有的事务进行定序。Spanner的这个做法没有利用任何的happens-before关系，和纯粹的Vector Clock是两个极端。

## Logical Physical Clocks
还有一种混合逻辑时钟Logical Physical Clocks，也是就逻辑时钟演变而来，但是时钟的值比较接近物理时钟，而且不依赖于物理时钟同步算法，它的时间戳截取也在本地，而且取出来是个时间值。

## Reference
- [Lamport clocks](http://amturing.acm.org/p558-lamport.pdf)
- [Vector clock](https://en.wikipedia.org/wiki/Vector_clock)
- [Dynamo](http://www.allthingsdistributed.com/files/amazon-dynamo-sosp2007.pdf)
- [分布式数据库中为什么要使用 Vector Clock？](https://www.zhihu.com/question/19994133)
- [Why Cassandra doesn’t need vector clocks](http://www.datastax.com/dev/blog/why-cassandra-doesnt-need-vector-clocks)
- [Why Vector Clocks are Easy](http://basho.com/posts/technical/why-vector-clocks-are-easy/)
- [Why Vector Clocks Are Hard](http://basho.com/posts/technical/why-vector-clocks-are-hard/)
- [Vector Clocks Revisited](http://basho.com/posts/technical/vector-clocks-revisited/)
- [Logical Physical Clocks
and Consistent Snapshots in Globally Distributed Databases](https://www.cse.buffalo.edu/tech-reports/2014-04.pdf)
