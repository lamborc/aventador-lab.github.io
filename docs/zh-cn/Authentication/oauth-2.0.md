# OAuth 2.0

> 一种授权机制

最简单的场景: 外卖送餐要进门禁,一般业主是刷卡或输密码开门,但为了安全不想告诉密码,外面可以按门禁呼叫功能.

在实际业务系统中:token 和password 都能实现登录,但一般会设计的权限范围不同.

## OAuth2.0 授权方式

> OAuth2.0 的授权简单理解其实就是获取令牌（token）的过程，OAuth 协议定义了四种获得令牌的授权方式（authorization grant ）

- 授权码（authorization-code）[]
- 隐藏式（implicit）[access_token]
- 密码式（password）[不安全,如第三方恶意留存密码,非常危险]
- 客户端凭证（client credentials）[一般用于没有前端交互UI的场景]
