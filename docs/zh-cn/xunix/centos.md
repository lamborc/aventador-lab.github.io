# Centos 基础命令

## 查看系统信息

```bash
cat /proc/version # /proc/cpuinfo meminfo
cat /etc/redhat-release  #
```

## Centos U盘安装

### dracut-pre-udev[xxx]  could not inset 'edd' 问题解决思路

> 启动进入选择界面 按 e 

先修改如下: initrd=initrd.img linux dd 用来获取U盘名称

```bash
setparams 'Install CentOS 7'
        linuxefi /images/pxeboot/vmlinuz initrd=initrd.img linux dd quiet
        initrdefi /images/pxeboot/initrd.img
```

按ctrl+x 重启 再次输入 e 进入修改命令行


```bash
setparams 'Install CentOS 7'
        linuxefi /images/pxeboot/vmlinuz inst.stage2=hd:/dev/sba2 quiet
        initrdefi /images/pxeboot/initrd.img
```

按 ctrl+x 重启安装.


### 安装界面分区

在

## 查看挂载
