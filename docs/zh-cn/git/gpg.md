## GUN Privacy Guard

|symbol| Capability /Usage| Description|
| :--: | :--: | :-- | 
| [C] | Certificating | 认证其他密钥,给其他证书签名,like SSL/TLS |
| [S] | Signing | 给数据签名 |
| [A] | Authenticating | 身份验证/鉴权 |
| [E] | Encrypting | 加密数据 |


> 加密过程

sender: (use public key) encrypted data ===> receiver decrypted data (use private key) 

> 签名过程

- data sha-256 ==> hash ()
- sender (use private key): signed hash+data ==> signature
- receiver : sha-256 data ===> hash
- receiver: comparation hash === signature  verified

#### GPG subprivatekey

> GPG 非常明智地设计了 subkey 机制，让用户免去给自己设计证书信任链的麻烦

![](../../assets/gpg.jpg)

- 主私钥必须有【认证 [C]】这个能力，且这项能力只能属于主私钥。
- 主私钥可以同时拥有【认证 [C]】、【签名 [S]】、【身份验证 [A]】三项能力。
- 子私钥可以同时拥有【签名 [S]】、【身份验证 [A]】两项能力。
- 【加密 [E]】能力必须属于独立的子私钥，因为加密用的是不同的算法变体（和签名不一样）

#### 算法

> Asymmetric encryption algorithm

| algorithm | security | performance | maturity |
| :--: | :--: | :--: | :--: |
| RSA 4096 | 4000 | 30 | high |
| Curve 25519 | 3000 | 1000 | low |
| RSA 2048 | 2000 | 100| high |

> Hash Algorithm Comparison

| algorithm | security | performance |
| :--: | :--: | :--: |
| SHA-256 | high | slow |
| MD5 | midium | fast |