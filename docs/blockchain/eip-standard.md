# EIP

## ERC20 


## E1155

## EIP 712

> EIP712Domain的结构体

- string name : 用户可读的签名域名的名称。例如Dapp的名称或者协议
- string version：签名域名的目前主版本。不同版本的签名不兼容
- uint256 chainId：EIP-155中的链id。用户代理应当拒绝签名如果和目前的活跃链不匹配的话
- address verifyContract：验证签名的合约地址。用户代理可以做合约特定的网络钓鱼预防
- bytes32 salt：对协议消除歧义的加盐。这可以被用来做域名分隔符的最后的手段