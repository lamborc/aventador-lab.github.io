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

### Firewall

```bash
firewall-cmd --state  # 查看firewall的状态

# 开启
service firewalld start
# 重启
service firewalld restart
# 关闭
service firewalld stop

# 查看防火墙规则
firewall-cmd --list-all

# 查询端口是否开放
firewall-cmd --query-port=8080/tcp
# 开放80端口
firewall-cmd --permanent --add-port=80/tcp
# 移除端口
firewall-cmd --permanent --remove-port=8080/tcp
#重启防火墙(修改配置后要重启防火墙)
firewall-cmd --reload
```

- 添加port

```bash 
firewall-cmd --zone=public --add-port=80/tcp --permanent
firewall-cmd --zone=public --add-port=443/tcp --permanent
firewall-cmd --zone=public --add-port=37000-39999/tcp --permanent
firewall-cmd --zone=public --add-port=37000-39999/udp --permanent
firewall-cmd --reload

# iptables -I INPUT -p tcp --dport 2888 -j ACCEPT
sudo iptables -I INPUT -p tcp --dport 2888 -j ACCEPT
sudo service iptables save

```

1、firwall-cmd：是Linux提供的操作firewall的一个工具；
2、--permanent：表示设置为持久；
3、--add-port：标识添加的端口；


