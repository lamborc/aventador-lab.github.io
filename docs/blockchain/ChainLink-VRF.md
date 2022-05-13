# Chainlink

> Chainlink包括 Verifiable Random Numbers (VRF)、Call External APIs、Chainlink Keepers。当然，使用最广泛的还是价格预言机，叫 Data Feeds。

> Chainlink Data Feeds 目前已经支持了多条链，主要还是 EVM 链，包括 Ethereum、BSC、Heco、Avalanche 等，也包括 Arbitrum、Optimism、Polygon 等 L2 的链。另外，也支持了非 EVM 链，目前支持了 Solana 和 Terra

----

## DeFi 应用接入使用 Chainlink Data Feeds

> Price Feed

```solidity 
/ SPDX-License-Identifier: MIT
pragma solidity ^0.8.7;

import "@chainlink/contracts/src/v0.8/interfaces/AggregatorV3Interface.sol";

contract PriceConsumerV3 {

    AggregatorV3Interface internal priceFeed;

    /**
     * Network: Kovan
     * Aggregator: ETH/USD
     * Address: 0x9326BFA02ADD2366b30bacB125260Af641031331
     */
    constructor() {
        priceFeed = AggregatorV3Interface(0x9326BFA02ADD2366b30bacB125260Af641031331);
    }

    /**
     * Returns the latest price
     */
    function getLatestPrice() public view returns (int) {
        (
            uint80 roundID, 
            int price,
            uint startedAt,
            uint timeStamp,
            uint80 answeredInRound
        ) = priceFeed.latestRoundData();
        return price;
    }
}
```

## ChainLink VRF 

> ChainLink VRF  是一个可证明的公平和可验证的随机性来源,

> 开发者可以使用它作为防篡改的随机数生成器，为依赖于不可预知结果的以太坊应用程序构建安全可靠的智能合约。关键的是，ChainLink VRF不仅提供随机数，而且还提供了加密证明，证明该数字没有被篡改以产生某些可预测的结果

- 智能合约会向Chainlink或Chainlink预言机网络提供一个seed来请求随机数。这个seed是预言机无法预测的，会被用来生成一个随机数。每个预言机都会使用自己专属的密钥生成随机数。
- 当结果和证明在链上发布后，可以使用预言机的公钥和智能合约的seed进行验证。这个方法利用了区块链著名的签名验证功能，合约只能使用在同一区块链环境中被验证通过的随机数

### Chainlink VRF的工作原理

![VRF的工作原理](../../assets/img/chainlink-work-remark.png)

- 首先，智能合约应用，也就是我们的Dapp，需要先发起一个获取随机数的请求，这个请求需要给定一个合约地址，这个合约称为VRFCoordinator合约。
- 与VRFCoordinator合约所关联的Chainlink链下节点，会（通过椭圆曲线数字签名算法）生成一个随机数，以及一个证明。
- Chainlink节点将上面生成的随机数和证明发送到VRFCoordinator合约中。
- VRFCoordinator合约收到随机数和证明后，会对通过证明来验证所生成随机数的合法性。
- 随机数验证成功后，会将随机数发送回用户的智能合约应用

[](https://segmentfault.com/a/1190000022811227)

```soldility
/**
 * 复写这个函数的时候就可以添加一些自己的业务逻辑代码
 * 
 */
function fulfillRandomness(bytes32 requestId, uint256 randomness)
    external virtual;

/**
 * _keyHash :  节点的公钥哈希 ,chainlink 查(不同network,不同)
 * _fee: 用户发起一次VRF请求所需要花费的费用(节点有最低额限制)
 * _seed : 用户提供的种子，用于生成随机数。用户需要给出高质量的种子。这里我们需要解释一下VRF的特点，VRF是通过种子与节点私钥做椭圆曲线中的计算得来的，同一个种子对应的随机数也是相同的，所以用户需要每次都给出不一样的且不可预测的种子。这很重要。因为任何可以左右用户种子的因素，都可以与链下的节点勾结作恶，生成他们想要的随机数，从而损害用户的利益。区块链中，我们很容易就找到这么一个随机源就是区块哈希，但是区块哈希是可以被矿工控制的（虽然很难），所以建议不能仅使用区块链哈希，还需要与其他随机源一起使用生成种子
 */
function requestRandomness(bytes32 _keyHash, uint256 _fee, uint256 _seed)
    public returns (bytes32 requestId)

bytes32 internal constant keyHash = 0xced103054e349b8dfb51352f0f8fa9b5d20dde3d06f9f43cb2b85bc64b238205;
uint256 internal constant fee = 10 ** 18;
// 需要发起VRF请求来获取随机数
getRandomness(userProvidedSeed){
	require(LINK.balanceOf(address(this)) > fee, "Not enough LINK - fill contract with faucet");
    uint256 seed = uint256(keccak256(abi.encode(userProvidedSeed, blockhash(block.number)))); // Hash user seed and blockhash
    bytes32 _requestId = requestRandomness(keyHash, fee, seed);
    return _requestId;
}

/**
 * 开奖函数 
 */
function fulfillRandomness(bytes32 requestId, uint256 randomness) 
    external override {
    	// max:20,min: 是参与Game 游戏的人数,(min < num <=20)
    random = randomness.mod(20).add(1);
}
```