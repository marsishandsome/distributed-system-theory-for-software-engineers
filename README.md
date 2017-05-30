# Distributed System Theory for Software Engineers

## First Step
- [ ][Distributed systems for fun and profit by Mikito Takada](http://book.mixu.net/distsys/)
- [ ][Notes on Distributed Systems for Young Bloods](https://www.somethingsimilar.com/2013/01/14/notes-on-distributed-systems-for-young-bloods/)
- [ ][A Note on Distributed Computing](http://citeseerx.ist.psu.edu/viewdoc/summary?doi=10.1.1.41.7628)
- [ ][Fallacies of distributed computing](https://en.wikipedia.org/wiki/Fallacies_of_distributed_computing)

## Failure and Time
- The (partial) hierarchy of failure modes
  - [x] [FAILURE MODES IN DISTRIBUTED SYSTEMS](http://alvaro-videla.com/2013/12/failure-modes-in-distributed-systems.html)

- How you decide whether an event happened before another event in the absence of any shared clock
  - [x] [Lamport clocks](http://amturing.acm.org/p558-lamport.pdf)
  - [x] [Vector clock](https://en.wikipedia.org/wiki/Vector_clock)
  - [x] [分布式数据库中为什么要使用 Vector Clock？](https://www.zhihu.com/question/19994133)

- How big an impact the possibility of even a single failure can actually have on our ability to implement correct distributed systems
  - [ ][ELP](http://www.slideshare.net/HenryRobinson/pwl-nonotes)
- Different models of time: synchronous, partially synchronous and asynchronous
  - [ ]TODO

## The basic tension of fault tolerance
- The quorum technique for ensuring single-copy serializability
  - [ ]<a href="https://en.wikipedia.org/wiki/Quorum_(distributed_computing)">Quorum wikipedia</a>
  - [ ][A Quorum-based Commit Protocol](https://ecommons.cornell.edu/bitstream/handle/1813/6323/82-483.pdf?sequence=1)
- different fault-tolerance properties
  - [x] [Two-Phase Commit](http://the-paper-trail.org/blog/consensus-protocols-two-phase-commit/)
  - [x][Three-phase Commit](http://the-paper-trail.org/blog/consensus-protocols-three-phase-commit/)
  - [ ] [Increasing the Resilience of Distributed and Replicated Database Systems](http://webee.technion.ac.il/~idish/Abstracts/jcss.html)
  - [ ] [Analysis and Verification of Two-Phase Commit & Three-Phase Commit Protocols](http://www.win.tue.nl/~atif/reports/paper4ICET.pdf)
  - [ ][Sinfonia: a new paradigm for building scalable distributed systems](http://www.sosp2007.org/papers/sosp064-aguilera.pdf)
  - [ ][Paxos](http://the-paper-trail.org/blog/consensus-protocols-paxos/)
- How eventual consistency, and other techniques, seek to avoid this tension at the cost of weaker guarantees about system behaviour
  - [Dynamo](http://www.allthingsdistributed.com/files/amazon-dynamo-sosp2007.pdf)
  - [Life Beyond Transactions](http://adrianmarriott.net/logosroot/papers/LifeBeyondTxns.pdf)

## Basic primitives
- Leader election
  - [Bully algorithm](https://en.wikipedia.org/wiki/Bully_algorithm)
- Consistent snapshotting
  - [Distributed Snapshots: Determining Global
States of Distributed Systems ]((https://www.cs.princeton.edu/courses/archive/fall16/cos418/docs/P8-chandy-lamport.pdf)
- Consensus
  - 2PC
  - Paxos
- Distributed state machine replication
  - [State machine replication wikipedia](https://en.wikipedia.org/wiki/State_machine_replication)
  - [Lampson’s paper](https://www.microsoft.com/en-us/research/publication/how-to-build-a-highly-available-system-using-consensus/?from=http%3A%2F%2Fresearch.microsoft.com%2Fen-us%2Fum%2Fpeople%2Fblampson%2F58-consensus%2Facrobat.pdf)

## Fundamental Results
- CAP Theorem
- [Papers We Love SF talk](https://www.slideshare.net/HenryRobinson/pwl-nonotes)

## Real systems

### Google
- [GFS](http://static.googleusercontent.com/media/research.google.com/en/us/archive/gfs-sosp2003.pdf)
- [Spanner](http://static.googleusercontent.com/media/research.google.com/en/us/archive/spanner-osdi2012.pdf)
- [F1](http://static.googleusercontent.com/media/research.google.com/en/us/pubs/archive/41344.pdf)
- [Chubby](http://static.googleusercontent.com/media/research.google.com/en/us/archive/chubby-osdi06.pdf)
- [BigTable](http://static.googleusercontent.com/media/research.google.com/en/us/archive/bigtable-osdi06.pdf)
- [MillWhell](http://static.googleusercontent.com/media/research.google.com/en/us/pubs/archive/41378.pdf)
- [Omeg](http://eurosys2013.tudos.org/wp-content/uploads/2013/paper/Schwarzkopf.pdf)
- [Dapper](http://static.googleusercontent.com/media/research.google.com/en/us/pubs/archive/36356.pdf)
- [Paxos Made Live](http://www.cs.utexas.edu/users/lorenzo/corsi/cs380d/papers/paper2-1.pdf)
- [The Tail At Scale](https://research.google.com/pubs/pub40801.html)

### Not Google
- [Dryad](http://research.microsoft.com/en-us/projects/dryad/eurosys07.pdf)
- [Cassandra](https://www.cs.cornell.edu/projects/ladis2009/papers/lakshman-ladis2009.pdf)
- [Ceph](http://ceph.com/papers/weil-ceph-osdi06.pdf)
- [RAMCloud](https://ramcloud.stanford.edu/wiki/display/ramcloud/RAMCloud+Papers)
- [HyperDex](http://hyperdex.org/papers/)
- [PNUTS](http://www.mpi-sws.org/~druschel/courses/ds/papers/cooper-pnuts.pdf)

## Reference
- [machine-learning-for-software-engineers](https://github.com/ZuzooVn/machine-learning-for-software-engineers)
- [Distributed systems theory for the distributed systems engineer](http://the-paper-trail.org/blog/distributed-systems-theory-for-the-distributed-systems-engineer/)
