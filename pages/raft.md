- 分布式共识算法
- 设计初衷
  与Multi-Paxos共识算法相比，更加便于理解和学习.
  Raft 是用来管理复制日志（replicated log）的一致性协议。它跟 multi-Paxos 作用相同，效率也相当，但是它的组织结构跟 Paxos 不同。这使得 Raft 比 Paxos 更容易理解并且更容易在工程实践中实现。为了使 Raft 协议更易懂，Raft将一致性的关键元素分开，如 leader 选举、日志复制和安全性，并且它实施更强的一致性以减少必须考虑的状态的数量.用户研究的结果表明，Raft 比 Paxos 更容易学习。 Raft 还包括一个用于变更集群成员的新机制，它使用重叠的大多数（overlapping majorities）来保证安全性。
- 介绍
  Consensus algorithms allow a collection of machinesto work as a coherent group that can survive the fail-ures of some of its members. 
  一致性算法允许多台机器作为一个集群协同工作，并且在其中的某几台机器出故障时集群仍然能正常工作。 正因为如此，一致性算法在建立可靠的大规模软件系统方面发挥了关键作用。
  在过去十年中，Paxos [15,16] 主导了关于一致性算法的讨论：大多数一致性的实现都是基于 Paxos 或受其影响，Paxos 已成为用于教授学生一致性相关知识的主要工具。
  Paxos缺点：实在是太难以理解--->所以设计了Raft一致性算法
- 资料
  reft白皮书论文
  [Raft论文翻译](https://willzhuang.github.io/2018/03/04/Raft%E8%AE%BA%E6%96%87%E7%BF%BB%E8%AF%91/)
  ![raft白皮书英文版.pdf](../assets/raft_1656487750310_0.pdf)
-
-