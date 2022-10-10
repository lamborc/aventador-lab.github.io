# Git Message 规范

> commit message格式 

```bash
<type>(<scope>): <subject>
```

**type(必须)**

- upd：更新某功能（不是 feat, 不是 fix）
- feat：新功能（feature）
- fix/to：修补 bug,fix 产生diff并自动修复此问题。适合于一次提交直接修复问题,to: 只产生diff不自动修复此问题。适合于多次提交
- docs：文档（documentation）
- style： 格式（不影响代码运行的变动）
- refactor：重构（即不是新增功能，也不是修改 bug 的代码变动）
- test：增加测试
- chore：构建过程或辅助工具的变动
- perf：优化相关，比如提升性能、体验。
- revert：回滚到上一个版本
- merge：代码合并。
- sync：同步主线或分支的Bug。

**scope(可选)**

  scope用于说明 commit 影响的范围

**subject(必须)**

  subject是commit目的的简短描述，不超过50个字符


```bash
fix(DAO):用户查询缺少username属性 
feat(Controller):用户查询接口开发
```

#### Checkout from tag and fixed bug workflow

```bash
git tag -l  # 列出分支 
git checkout v3.0.2  # checkout tag 
git checkout -b 3.0.2  # create local fixed bug branch 
git diff develop  src/xxx/xxx.js  # 对比文件差异
git commit -m "fixed bug #xxx" -a  # fixed bug and commit branch
git checkout develop #
git pull # let develop Keep consistent remote
git merge 3.0.2  # 合并分支 
git push  # 
git branch -D 3.0.2 # 删除本地分支

```

> 
git log -n 1 | head -n 1 | sed -e 's/^commit //' | head -c 8 

