# Solana 
> A new architecture for a high performance blockchain

**base on** version: 0.8.13

### Introduction

> 区块链是一种容错复制状态机的实现。当前公开可用的区块链不依赖于时间，或使关于参与者保持时间能力的假设 [4, 5]。 中的每个节点网络通常依赖于他们自己的本地时钟而不知道任何网络中的其他参与者时钟。 缺乏可靠的时间来源表示当使用消息时间戳来接受或拒绝消息时，不能保证网络中的每个其他参与者都会完全相同的选择。 这里展示的 PoH 旨在创建一个账本具有可验证的时间流逝，即事件和消息之间的持续时间订购。 预计网络中的每个节点都将能够依赖在没有信任的情况下记录在分类帐中的时间流逝

### Outline


### Network Design

> As shown in Figure 1, at any given time a system node is designated as
Leader to generate a Proof of History sequence, providing the network global
read consistency and a verifiable passage of time. The Leader sequences user
messages and orders them such that they can be efficiently processed by other
nodes in the system, maximizing throughput. It executes the transactions
on the current state that is stored in RAM and publishes the transactions
and a signature of the final state to the replications nodes called Verifiers.
Verifiers execute the same transactions on their copies of the state, and pub-
lish their computed signatures of the state as confirmations. The published
confirmations serve as votes for the consensus algorithm



