## Git branch

> 使用Git创建本地分支，并push到远程

``` bash
  - git branch -a                 //查看所有分支
  - git status                  //查看状态
  - git checkout -b newBranch           //新建本地分支
  - git branch -d brachname         //删除本地分支
  - git push origin newBranch[:newBranch]     //创建远程分支，或者
  - git push --set-upstream origin sol5     //管理本地默认推送分支，直接git push
  - git push origin :newBranch          //通过推送空分支的方式删除远程分支
  - git push origin --delete newBranch      //删除远程分支
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

> commit -a 参数可以将所有已跟踪文件中的执行修改或删除操作的文件都提交到本地仓库，即使它们没有经过git add添加到暂存区,建议先 git add ./ 再 git commit -m 'comments'

```bash
git tag -l                                  # 列出分支 
git checkout v3.0.2                         # checkout tag 
git checkout -b 3.0.2                       # create local fixed bug branch 
git diff develop  src/xxx/xxx.js            # 对比文件差异
git commit -m "fixed bug #xxx"              # fixed bug and commit branch
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

 ----

## Git 回滚 & 恢复

### 误删除本地文件恢复

```bash
git status # 
git reset HEAD <dir/file> # 针对目录或文件
git checkout src/ui
```

> git reset HEAD src/ui

```bash
M       src/store/index.js
D       src/store/initial-p3-state.js
D       src/ui/scss/_all.scss
D       src/ui/scss/_brv-core.scss
D       src/ui/scss/_mixin.scss
D       src/ui/scss/_reset.scss
D       src/ui/scss/_utilities.scss
D       src/ui/scss/_variables.scss
D       src/ui/scss/utils/classname-util.js
M       src/widgets/select/select.scss
```

### Git reset back commit hash

#### 方式一

> Git 回滚: 强制 push 方式

- 0: 如本地有未提交代码,可git checkout -b for-reset-tmp && git add . && git commit -am 'xxxx' && git checkout current branch
- 1: git pull 保证本地与remote 一致
- 2: 备份当前分支(如有必要)
- 3: git log [--pretty=online] 查找要回复的hash
- 4: git reset --hard <commit hash>
- 5: git push -f origin <branch name>  # 强制更新远程分支

#### 方式二

> 从回滚位置生成新的commit hash


- 0: 如本地有未提交代码,可git checkout -b for-reset-tmp && git add . && git commit -am 'xxxx' && git checkout current branch
- 1: git pull   # 保证当前工作区是干净的，并且和远程分支代码一致
- 2: 备份分支(如有必要)
- 3: git revert <hash> # 不加 --no-commit 生成新的hash
- 4: git push

## Git merge

> 假设要将develop 分支最新内容合并到master 分支

- git checkout develop            //切换至develop分支
- git pull 或git fetch
- git checkout master

### git pull 和 git fetch

> FETCH_HEAD： 是一个版本链接，记录在本地的一个文件中，指向着目前已经从远程仓库取下来的分支的末端版本

> commit-id ：  在每次本地工作完成后，都会做一个git commit 操作来保存当前工作到本地的repo， 此时会产生一个commit-id，这是一个能唯一标识一个版本的序列号。 在使用git push后，这个序列号还会同步到远程仓库

> git fetch :在每次本地工作完成后，都会做一个git commit 操作来保存当前工作到本地的repo， 此时会产生一个commit-id，这是一个能唯一标识一个版本的序列号。 在使用git push后，这个序列号还会同步到远程仓库


> 最后总结：可以理解为git pull 是把git fetch 和git merge 合并的操作，当然有冲突是可能会失败 

#### 通过git fetch 更新远程仓库

  - git fetch origin master[:tmp]       //将远程master更新至本地master[:tmp]省略即同名(本地叫master)
  - git diff tmp              //对比本地当前分支与下载tmp区别
  - git merge tmp             //用tmp 合并本地当前分支
  - git branch -d tmp             //删除本地临时分支

> 对比两次提交具体文件差别

``` bash
git log    // 查看commit list 
git diff hash1 hash2 --stat         // 对比所有更改
git diff hash hash ./file 
```