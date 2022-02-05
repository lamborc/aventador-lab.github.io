# Certbot Classic


### Prevous workflow 
> Centos7 snap : 新一代包管理工具 snap 安装

> sudo yum install net-tools vim

```bash
yum install epel-release
yum install snapd
systemctl enable --now snapd.socket # 添加snap启动通信 socket
ln -s /var/lib/snapd/snap /snap # 创建链接（snap软件包一般安装在/snap目录下）非常重要
sudo snap install core     # 
sudo snap refresh core     # 
```

### 安装 Certbot classic

> 安装前先卸载

```bash
sudo snap install --classic certbot
sudo ln -s /snap/bin/certbot /usr/bin/certbot
sudo certbot --nginx  # 自动申请证书
sudo certbot  -d www.baschain.cn   # 手动申请证书
```


