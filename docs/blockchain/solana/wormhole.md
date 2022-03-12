# Wormhole Native Swap

> 我们有能力跨多个不同的区块链执行从一个原生代币到另一个原生代币的一键交换

## Step by Step: How To Transfer Binance Assets to Solana Using Wormhole V2

### How to deposit SOL

wormhole 大致实现逻辑是这样的：

1. wormhole 在各大主链上deploy 一套管理合约
 - 合约会将用处转token,先锁定，等target链确认交易后再确认转入wormhole 项目，如果target 链确认失败，用户可以redeem 回自己账号，整个过程会产生交易费。
 - 这里管理合约可以理解为银行，每笔交易可看做用户签发了一张支票（Cashier's Check 银行本票）。
2. 用户跨链交易时，先在转出链上（例如ethereum)，将要转出的Token 转到wormhole 在ethereum的管理合约上
3. 然后wormhole node节点(可以理解为中心化服务，白皮书说是去中心化的)会收到转账交易通知
4. wormhole 根据交易通知在target 链上（如 BSC）将对应的token 数量转给 用户地址
5. wormhole 再通知转出链合约转账完成（这样用户就不能再reddeem了，2月份wormhole 被攻击好像就是利用的这个时间差，和节点交易信息确认没同步导致的）
 

