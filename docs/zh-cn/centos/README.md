# Centos Series


## Profile 

> System wide environment and startup programs, for login setup

It's NOT a good idea to change this file unless you know what you are doing. It's much better to create a custom.sh shell script in /etc/profile.d/ to make custom changes to your environment, as this will prevent the need for merging in future updates.

### 添加环境变量的正确姿势

> 以nodejs 为例

nodejs 目录 : /usr/src/node14

>> 在/etc/profile.d/sh.local 

#Add any required envvar overrides to this file, it is sourced from /etc/profile

**vim sh.local**

```bash
NODE_HOME=/usr/src/node14
PATH=$PATH:$NODE_HOME/bin
```

**load source** 设置生效

```bash
source /etc/profile
```
