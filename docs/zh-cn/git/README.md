# Git

> ssh key: ssh-keygen -t ed25519 -C 'comment'

## SSH VS GPG

- SSH (Secure Shell) 用于交互通信过程中的安全，是双向的
- GPG (GNU Privacy Guard) 既可用于加密，也可用于签名，这些都是单向的
- 对于 SSH，建议一站一钥，万一丢了也好处理
- 对于 GPG，一把主钥＋多个子钥即可


## Managing Signature

> 生成GPG 密钥对

```bash
gpg --full-generate-key

```