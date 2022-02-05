# 常用命令


### 系统命令

cat /etc/issue

cat /etc/redhat-rlease

### 查看文件权限数值

```bash
stat -c '%n %a %U:%G' . * 
chmod 600 authorized_keys  
```

### Tee

> ee 命令用于将数据重定向到文件，另一方面还可以提供一份重定向数据的副本作为后续命令的 stdin。简单的说就是把数据重定向到给定文件和屏幕上

![](/assets/img/centos/tee_command.png)

```bash
sudo ls -al /root | sudo tee /root/test.txt > /dev/null  # sudo ls -al /root | sudo tee /root/test.txt > /dev/null

```

其中**> /dev/null** 阻止 tee 把内容输出到终端.

## 基础命令

### 权限相关

- 更改文件或目录所属用户组

```bash
chgrp -R <user> <dir> 
chown -R own:user <dir>
```

  - R 表示递归


 