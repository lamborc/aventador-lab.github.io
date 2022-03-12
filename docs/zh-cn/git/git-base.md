# Git Base Commands

## 管理远程仓库

> git remote -v

```bash
git remote -v  # Verify new remote
git remote set-url <--add|--push|--delete> origin url
```

#### Fork project sync origin

- git remote -v : check [origin:本地, upstream:远程]
- git remote add upstream <原项目 url>
- git fetch upstream 将远程取到本地
- git checkout master 检查本地变更
- git merge upstream/master 合并分支

#### Git set proxy

```bash
git config --global https.proxy http://127.0.0.1:8080
git config https.proxy   # view
git config --global --unset https.proxy # cancle proxy

git config --global http.proxy 'socks5://127.0.0.1:10808'
git config --global https.proxy 'socks5://127.0.0.1:10808'
```


> 设置代理

```bash
git config  http.proxy socks5://127.0.0.1:1080

git config --global --unset http.proxy  # 取消代理
```

#### .gitconfig 用户级git配置

> ~/.gitconfig (对于unix 还有系统级 /etc/.gitconfig)

> 项目级配置文件 .get/config 

> git remote add origin https://github.com/xxx/abc.git 是向配置中设置远程仓库地址.

### 如果你通过ssh公钥访问私有仓库，记得配置git拉取私有仓库时使用ssh

> 可以通过命令git config ...的方式来配置

> ~/.gitconfig

```bash
git config url."git@xxx:abc/cd".insteadof "https://github.com/abc/cd"  添加到

[url "git@github.com:"]
    insteadOf = https://github.com/
[url "git@gitlab.com:"]
    insteadOf = https://gitlab.com/
```

### 修改 hosts 文件方式

> windows ipconfig /flushdns 可使修改立即生效