# Ethereum

## Ethereum 2.0 Keys

>  Boneh-Lynn-Shacham  BLS

![](../../assets/img/ethereum/ethereum-2-bls-keys.png)

## PROOF-OF-STAKE (POS) 共识机制

> 权益证明对工作量证明作了5点改进

- better energy efficiency there is no need to use lots of energy on proof-of-work computations 更好的能源效率不需要在工作量证明计算上使用大量的能量

> 降低能源消耗 

- ower barriers to entry, reduced hardware requirements there is no need for elite hardware to stand a chance of creating new blocks 更低的进入门槛，更低的硬件要求无需精英硬件就有机会创造新的区块

> 降低硬件门槛

- reduced centralization risk proof-of-stake should lead to more nodes securing the network 集中化风险风险证明的减少应该会导致更多的节点保护网络



- because of the low energy requirement less ETH issuance is required to incentivize participation 由于能源需求低，需要较少的ETH来激励参与 （理论降低Gas ）


- economic penalties for misbehaviour make 51% style attacks exponentially more costly for an attacker compared to proof-of-work 与工作量证明相比，对行为不当的经济惩罚使51%风格的攻击对攻击者来说代价呈指数级上升

> 51% 攻击难度指数级增加

- the community can resort to social recovery of an honest chain if a 51% attack were to overcome the crypto-economic defenses
 如果51%的攻击要克服加密经济防御，社区可以求助于诚实链的社会恢复

> 

## VALIDATORS 验证器

 要作为验证者参与，用户必须在存款契约中存入32个ETH，并运行三个独立的软件:执行客户端、共识客户端和验证者。在存入他们的ETH后，用户加入一个激活队列，该队列限制了新验证器加入网络的速率。一旦激活，验证器将从以太坊网络上的对等体接收新块。重新执行在块中交付的事务，并检查块签名以确保块有效。然后，验证器发送一个投票(称为认证)，

 在工作量证明中，区块的时间由采矿难度决定，而在权益证明中，区块的速度是固定的。权益证明以太坊的时间分为插槽(12秒)和epoch(32个插槽)。在每个槽位随机选择一个验证器作为块提议器。该验证器负责创建新块并将其发送到网络上的其他节点。在每个插槽中，随机选择一个验证者委员会，他们的投票用于确定所提议的块的有效性


## FORK CHOICE 硬分叉
当网络以最佳和诚实的方式运行时，链的顶端只有一个新块，所有验证器都会证明这一点。然而，由于网络延迟或块提议者模棱两可，验证器可能对链头有不同的看法。因此，共识客户需要一个算法来决定支持哪一个。Ethereum股权证明中使用的算法称为LMD-GHOST，其工作原理是识别历史上证明权重最大的分叉

## GASPER 

Gasper是Casper the Friendly Finality Gadget（Casper FFG）和LMD-GHOST fork choice算法的组合。这些组件共同构成了确保以太坊股权证明的共识机制。Casper是一种将某些区块升级为“最终”区块的机制，以便网络中的新加入者能够确信他们正在同步规范链。分叉选择算法使用累积投票来确保当区块链中出现分叉时，节点可以轻松选择正确的分叉


## Keys (Account )

以太坊使用公私密钥加密技术保护用户的资产。公钥用作以太坊地址的基础，也就是说，它对公众可见，并用作唯一标识符。只有帐户所有者才能访问私钥（或“机密”）。私钥用于“签署”交易和数据，以便加密可以证明持有者批准特定私钥的某些操作

---- 
## THE ETHEREUM STACK

- LEVEL 1: ETHEREUM VIRTUAL MACHINE

- LEVEL 2: SMART CONTRACTS

- LEVEL 3: ETHEREUM NODES

- LEVEL 4: ETHEREUM CLIENT APIS

- LEVEL 5: END-USER APPLICATIONS


# Storage 

> DECENTRALIZED STORAGE


# Oracles 

在区块链和智能合约的背景下，Oracles是一个代理人，可以找到和验证真实世界的事件并将此信息提交给使用的区块链的智能合约。
智能合约包含价值，并且只有在某些预定义的条件得到满足时才解锁该价值 一个特定的值被达成，智能合约改变它的状态并执行编程预定义的算法， 自动触发区块链上的事件。Oracles的主要任务是提供这些值 以安全可靠的方式进行智能合约。
区块链不能访问网络外部的数据。Oracle是一种数据馈送 - 由第三方提供 - 专为在区块链上的智能合约中使用而设计，Oracles提供外部数据和当预定义的条件满足时触发智能合约执行，这种情况触发条件可以是任何数据，例如天气温度，成功支付，价格波动等。
Oracles是多重签名合约的一部分，例如受托人只有在符合某些条件的情况下才会签署有关未来资金释放的合约。在任何资金被释放之前，Oracle还必须签署智能合约