# Centos 常用配置



## 查看版本

```bash

```

## 配置固定IP

> Centois7不再使用ifconfig而是用ip  


```bash
ip addr

```

>vim /etc/sysconfig/network-scripts/ifcfg-eth0  

```text
# dhcp改为static   
BOOTPROTO="static" 

# 开机启用本配置  
ONBOOT="yes" 

# 静态IP  
IPADDR=192.168.29.128 

# 默认网关
GATEWAY=192.168.29.2  

# 子网掩码  
NETMASK=255.255.255.0 

# DNS 配置 
DNS1=192.168.29.2 
```

