# MetaMask


## Network

> 以太坊 Ethereum 主网络

- RPC URL:https://mainnet.infura.io/v3/9aa3d95b3bc440fa88ea12eaa4456161
- chainid: 1
- Symbol:ETH 
- exporole:https://etherscan.io

> Ropsten Testnet

- RPC URL:https://ropsten.infura.io/v3/9aa3d95b3bc440fa88ea12eaa4456161
- chainid: 3
- Symbol:ETH 
- exporole:https://ropsten.etherscan.io

> Rinkeby Testnet

- RPC URL:https://rinkeby.infura.io/v3/9aa3d95b3bc440fa88ea12eaa4456161
- chainid: 4
- Symbol:ETH 
- exporole:https://rinkeby.etherscan.io

> Goerli Testnet

- RPC URL:https://goerli.infura.io/v3/9aa3d95b3bc440fa88ea12eaa4456161
- chainid: 5
- Symbol:ETH 
- exporole:https://goerli.etherscan.io


> Kovan Testnet

- RPC URL: https://kovan.infura.io/v3/9aa3d95b3bc440fa88ea12eaa4456161
- chainid: 42
- Symbol:ETH 
- exporole: https://kovan.etherscan.io

----

> BSC-main

- RPC URL:https://bsc-dataseed.binance.org/
- chainid: 56
- Symbol:BNB
- exporole: https://bscscan.com

> BSC-TEST Testnet

- RPC URL: https://data-seed-prebsc-1-s1.binance.org:8545/
- chainid: 97
- Symbol: BNB
- exporole: https://testnet.bscscan.com

> Ropsten Testnet

- RPC URL:
- chainid: 1
- Symbol:ETH 
- exporole:

> Heco-main

- RPC URL: https://http-testnet.hecochain.com
- chainid: 256
- Symbol: HT
- exporole: https://scan-testnet.hecochain.com

> Heco-test

- RPC URL: https://http-testnet.hecochain.com
- chainid: 256
- Symbol: HT
- exporole: https://scan-testnet.hecochain.com


#### Sign

> web3 sign & contract verify

```solidity
function pclaim(address user, address pool, uint256 minerCredit, uint256 minerAmount, uint256 usedTraffic, bytes memory signature) public {
        userData memory ud = UserData[pool][user];
        require( usedTraffic >= ud.totalTraffic, "Claim traffic mismatch");
        bytes32 message = keccak256(abi.encode(this, token, user, pool, minerCredit, minerAmount, usedTraffic));
        bytes32 msgHash = prefixed(message);
        require(recoverSigner(msgHash, signature) == user);

        uint256 tn = (usedTraffic.sub(ud.totalTraffic)).mul(10**12).div(MBytesPerToken);
        uint256 left = ud.totalChargeBalance.sub(ud.totalTraffic.mul(10**12).div(MBytesPerToken));
        uint256 trs = tn;
        if (tn > left){
            trs = left;
            UserData[pool][user] = userData(ud.totalChargeBalance,usedTraffic.sub((tn.sub(left)).mul(MBytesPerToken).div(10**12)));
        }else{
            UserData[pool][user] = userData(ud.totalChargeBalance,usedTraffic);
        }
        token.transfer(msg.sender, trs);

        emit PoolClaim(pool, user, tn, left,trs, usedTraffic);

    }

    /// builds a prefixed hash to mimic the behavior of eth_sign.
    function prefixed(bytes32 hash) internal pure returns (bytes32) {
        return keccak256(abi.encode("\x19Ethereum Signed Message:\n32", hash));
    }
    function recoverSigner(bytes32 message, bytes memory sig) internal pure  returns (address) {
        (uint8 v, bytes32 r, bytes32 s) = splitSignature(sig);
        return ecrecover(message, v, r, s);
    }
    /// signature methods.
    function splitSignature(bytes memory sig) internal pure returns (uint8 v, bytes32 r, bytes32 s) {
        require(sig.length == 65);
        assembly {
        // first 32 bytes, after the length prefix.
            r := mload(add(sig, 32))
        // second 32 bytes.
            s := mload(add(sig, 64))
        // final byte (first byte of the next 32 bytes).
            v := byte(0, mload(add(sig, 96)))
        }
        return (v, r, s);
    }

```

> web3js

```js
export function packParams(
  web3js,
  { contractAddress, issueAddr, purchaseId, purchaseDays }
) {
  if (
    !web3js ||
    !contractAddress ||
    !issueAddr ||
    !purchaseId ||
    purchaseDays <= 0
  )
    throw new TypeError('parameters illegal.')

  return web3js.eth.abi.encodeParameters(
    ['address', 'address', 'bytes32', 'uint'],
    [contractAddress, issueAddr, purchaseId, purchaseDays]
  )
}
/**
 *
 * @param {Object} web3js
 * @param {String} selectedAddress required
 * @param {Object} params
 * @property contractAddress required
 * @property purchaseId required
 * @property purchaseDays required
 * @property issueAddress optional (default equal selectedAddress )
 *
 */
export async function signedLicense(web3js, selectedAddress, params = {}) {
  const { contractAddress, issueAddress, purchaseId, purchaseDays } = params
  if (
    !web3js ||
    !contractAddress ||
    !selectedAddress ||
    !purchaseId ||
    purchaseDays <= 0
  )
    throw new TypeError('parameters illegal.')

  const packData = packParams(web3js, {
    contractAddress,
    issueAddr: issueAddress || selectedAddress,
    purchaseId,
    purchaseDays,
  })

  const keccakPack = Web3Utils.keccak256(packData)
  const signedData = await web3js.eth.personal.sign(keccakPack, selectedAddress)
  const combo58 = compressLicense({
    issueAddr: issueAddress || selectedAddress,
    purchaseId,
    purchaseDays,
    signature: signedData,
  })
  // console.log('signedLicense>>>>>>>>>>>>', purchaseId, combo58)
  const orderParts = {
    contractAddress,
    purchaseId,
    issueAddress: issueAddress || selectedAddress,
    purchaseDays,
    signature58: combo58,
  }

  return orderParts
}

```