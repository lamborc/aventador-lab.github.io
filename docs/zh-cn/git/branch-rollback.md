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

### 从某一个commit开始创建本地分支  

```bash
# 查看 short hash
git log -n 1 | head -n 1 | sed -e 's/^commit //' | head -c 8 
git log 查看提交
git checkout commitId -b newbranch // 通过checkout 跟上commitId 即可创建制定commit之前的本地分支
```

### git stash

> stash 命令能够将还未 commit 的代码存起来，让你的工作目录变得干净。

> 某一天你正在 feature 分支开发新需求，突然产品经理跑过来说线上有bug，必须马上修复。而此时你的功能开发到一半，于是你急忙想切到 master 分支

```bash
# 保存当前未commit的代码
git stash

# 保存当前未commit的代码并添加备注
git stash save "备注的内容"

# 列出stash的所有记录
git stash list

# 删除stash的所有记录
git stash clear

# 应用最近一次的stash
git stash apply

# 应用最近一次的stash，随后删除该记录
git stash pop

# 删除最近的一次stash
git stash drop

# 当有多条 stash，可以指定操作stash，首先使用stash list 列出所有记录
git stash list 

# 应用第二条记录
git stash apply stash@{1}
···

### reset --soft

> 回退你已提交的 commit，并将 commit 的修改内容放回到暂存区。
一般我们在使用 reset 命令时，git reset --hard 会被提及的比较多，它能让 commit 记录强制回溯到某一个节点。而 git reset --soft 的作用正如其名，--soft (柔软的) 除了回溯节点外，还会保留节点的修改内容。

> 规范些的团队，一般对于 commit 的内容要求职责明确，颗粒度要细，便于后续出现问题排查。本来属于两块不同功能的修改，一起 commit 上去，这种就属于不规范。这次恰好又手滑了，一次性 commit 上去。

```bash
# 恢复最近一次 commit
git reset --soft HEAD^

# commit 记录有 c、b、a。
# reset 到a
git reset --soft 1a900ac29eba73ce817bf959f82ffcb0bfa38f75

# 此时的 HEAD 到了 a，而 b、c 的修改内容都回到了暂存区

···

### cherry-pick
> 给定一个或多个现有提交，应用每个提交引入的更改，为每个提交记录一个新的提交。这需要您的工作树清洁（没有从头提交的修改）

[教程](https://juejin.cn/post/7071780876501123085?share_token=fa5bbdb2-554f-4359-8bae-dfb15a5f3085)

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

-----

## 对比两次提交具体文件差别

```bash
git reflog # 查看版本提交记录

```

59f6605 HEAD@{4}: checkout: moving from dev-main to local-dev
9b3e815 HEAD@{5}: merge local-dev: Merge made by the 'recursive' strategy.

### 查看暂存区与具体某个版本之间的区别

git diff --cached bd71480 -- src/views/faq/faq-index.vue

- git diff 什么参数都不加，默认比较工作区暂存区的差异
- git diff --cached [<path>...]比较暂存区与最新本地版本库（本地库中最近一次commit的内容）
- git diff HEAD [<path>...]比较工作区与最新本地版本库。如果HEAD指向的是master分支，那么HEAD还可以换成master
- git diff commit-id [<path>...]比较工作区与指定commit-id的差异
- git diff --cached [<commit-id>] [<path>...]比较暂存区与指定commit-id的差异
- git diff [<commit-id>] [<commit-id>]比较两个commit-id之间的差异
- git diff commit-id1 commit-id2 --stat查看两个提交版本id修改了那些文件.
- git diff 版本号码1 版本号码2 src 比较两个版本的src 文件夹的差异
