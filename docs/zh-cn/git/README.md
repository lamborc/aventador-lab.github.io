# Git

## Github fork project sync origin

- git remote -v : check [origin:本地, upstream:远程]
- git remote add upstream <原项目 url>
- git fetch upstream 将远程取到本地
- git checkout master 检查本地变更
- git merge upstream/master 合并分支

## Git Message 规范

- upd：更新某功能（不是 feat, 不是 fix）
- feat：新功能（feature）
- fix：修补 bug
- docs：文档（documentation）
- style： 格式（不影响代码运行的变动）
- refactor：重构（即不是新增功能，也不是修改 bug 的代码变动）
- test：增加测试
- chore：构建过程或辅助工具的变动

### git set proxy

```bash
git config --global https.proxy http://127.0.0.1:8080
git config https.proxy   # view
git config --global --unset https.proxy # cancle proxy

git config --global http.proxy 'socks5://127.0.0.1:10808'
git config --global https.proxy 'socks5://127.0.0.1:10808'
```

### git Tag 

> 使用Git创建本地分支，并push到远程

```bash
git branch -a                 //查看所有分支
git status                  //查看状态
git checkout -b newBranch           //新建本地分支
git branch -d brachname         //删除本地分支
git push origin newBranch[:newBranch]     //创建远程分支，或者
git push --set-upstream origin sol5     //管理本地默认推送分支，直接git push
git push origin :newBranch          //通过推送空分支的方式删除远程分支
git push origin --delete newBranch      //删除远程分支
```

> list tag 

```bash
git tag
git tag -l 'v1.0.*' # 过滤
```

#### Checkout from tag and fixed bug workflow

```bash
git tag -l                                  # 列出分支 
git checkout v3.0.2                         # checkout tag 
git checkout -b 3.0.2                       # create local fixed bug branch 
git diff develop  src/xxx/xxx.js            # 对比文件差异
git commit -m "fixed bug #xxx" -a           # fixed bug and commit branch
git checkout develop                        #
git pull                                    # let develop Keep consistent remote
git merge 3.0.2                             # 合并分支 
git push                                    # 
git branch -D 3.0.2                         # 删除本地分支
```

> 新建标签

```bash
git tag -a v1.0.2 -m "comments"             # 新建带注释标签
git show v1.0.2                             # 查看标签信息
git log --pretty=oneline                    # 在后期对早先的某次提交加注标签。比如此示例展示的提交历史中

git push origin v1.0.2                      # 上传tag到远程

```

  - 验证标签 可以使用 git tag -v [tag-name] （译注：取 verify 的首字母）的方式验证已经签署的标签。此命令会调用 GPG 来验证签名，所以你需要有签署者的公钥，存放在 keyring 中，才能验证
  - 签署标签 如果你有自己的私钥，还可以用 GPG 来签署标签，只需要把之前的 -a 改为 -s （译注： 取 signed 的首字母）即可

 ```bash 
 git tag -s v1.5 -m 'my signed 1.5 tag'
 ```
 
  