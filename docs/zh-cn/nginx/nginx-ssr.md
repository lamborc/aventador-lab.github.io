# Nginx
> Centos 开放端口 

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
firewall-cmd --zone=public --add-port=28000-29999/tcp --permanent
firewall-cmd --zone=public --add-port=28000-29999/udp --permanent
firewall-cmd --reload
### 

- Template

```
server {
	server_name  blog.xxx.cc;
	index  index.html;
	# charset koi8-r;
	# access_log  logs/zzz.access.log access;
	proxy_set_header       Host $host;
	proxy_set_header  X-Real-IP  $remote_addr;
	proxy_set_header  X-Forwarded-For $proxy_add_x_forwarded_for;
	
	root         /usr/share/nginx/website;
	expires -1;
	####################################
	# proxy_pass实现了，关键是 proxy_set_header
	####################################
	location / {
		index  index.html;	
	}

	location /mis { 
		#/ray 路径需要和v2ray服务器端，客户端保持一致
		proxy_redirect off;
		proxy_pass http://127.0.0.1:57729; #此IP地址和端口需要和v2ray服务器保持一致，
		
		proxy_http_version 1.1;
		proxy_set_header Upgrade $http_upgrade;
		proxy_set_header Connection "upgrade";
		proxy_set_header Host $http_host;
		proxy_set_header X-Real-IP $remote_addr;
		proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
		proxy_connect_timeout 60s;
		proxy_read_timeout 86400s;
		proxy_send_timeout 60s;		
		
	}


	#############################
	# rewrite也可以转换，但url变了
	#########################
	#if ($uri ~* /bb/lib) {
	#         rewrite ^(.*) https://static.yyy.com/static$1 permanent;
	#}

    listen [::]:443 ssl ipv6only=on; # managed by Certbot
    listen 443 ssl; # managed by Certbot
    ssl_certificate /etc/letsencrypt/live/blog.lanbery.cc/fullchain.pem; # managed by Certbot
    ssl_certificate_key /etc/letsencrypt/live/blog.lanbery.cc/privkey.pem; # managed by Certbot
    include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot

}


server {
    if ($host = blog.lanbery.cc) {
        return 301 https://$host$request_uri;
    } # managed by Certbot


    listen       80;
	listen       [::]:80;
    server_name  blog.xxx.cc;
    return 404; # managed by Certbot
}
```

### index Default

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width,initial-scale=1.0" />
    <link rel="icon" href="favicon.ico" />
    <title>Welcome World</title>
    <link
      rel="stylesheet"
      href="https://fonts.googleapis.com/css?family=Roboto:100,300,400,500,700,900"
    />
    <link
      rel="stylesheet"
      href="https://cdn.jsdelivr.net/npm/@mdi/font@latest/css/materialdesignicons.min.css"
    />

    <style>
      .blog-error {
        display: flex;
        flex-direction: column;
        flex-wrap: nowrap;
        justify-content: center;
        align-items: center;

        color: #666;

      }
      .blog-error h3 {
        line-height: 3.5rem;
        padding-bottom: 1.25rem;
      }
    </style>
  </head>
  <body>
    <noscript>
      <strong>
        We're sorry but <%= VUE_APP_TITLE||htmlWebpackPlugin.options.title %>
        doesn't work properly without JavaScript enabled. Please enable it to
        continue
      </strong>
    </noscript>
	<div class="blog-error">
		<h3>Sorry,Some Error unfound</h3>
		<div>Wait a later!</div>
	</div>
  </body>
  
</html>

```

### SSL 


> Centos7 snap : 新一代包管理工具 snap 安装

> sudo yum install net-tools vim

```bash
yum install epel-release
yum install snapd
systemctl enable --now snapd.socket # 添加snap启动通信 socket
systemctl enable --now snapd.socket # 创建链接（snap软件包一般安装在/snap目录下）非常重要
sudo snap install core     # 
sudo snap refresh core     # 
ln -s /var/lib/snapd/snap /snap # 创建链接
```



- 申请证书
```bash
sudo snap install --classic certbot
sudo ln -s /snap/bin/certbot /usr/bin/certbot
sudo certbot --nginx  # 自动申请证书
sudo certbot  -d blog.xxx.cc   # 手动申请证书
```

- 自动更新证书

- crontab -l :查看
- crontab -e : 添加

```bash
# 每月的 1,7,21,28号， 4点30 更新证书
30 4 1,7,21,28 * * /usr/bin/certbot renew
# 每月的 1,7,21,28号， 5点30 重新启动nginx 服务器
30 5 1,7,21,28 * * /usr/sbin/nginx -t && /usr/sbin/nginx -s reload
```


#### configuration

- default path: /usr/local/etc/v2ray


```json
{
  "log":{
	"error":"/var/log/v2ray/error.log",
	"loglevel":"warning"
  },
  "inbounds": [{
    "port": 57729,
    "listen":"127.0.0.1",
    "protocol": "vmess",
    "settings": {
      "clients": [
        {
          "id": "3aec5b77-8717-4796-be7b-d87e0621c5f6",
          "level": 1,
          "alterId": 0
        }
      ]
    },
    "streamSettings": {
      "network": "ws",
      "wsSettings": {
        "path": "/mis"
      }
    }
  }],
  "outbounds": [
	  {
		"protocol": "freedom",
		"settings": {}
	  },
	  {
		"protocol": "blackhole",
		"settings": {},
		"tag": "blocked"
	  }
  ],
  "routing": {
    "rules": [
      {
        "type": "field",
        "ip": ["geoip:private"],
        "outboundTag": "blocked"
      }
    ]
  }
}

```

### Window Terminal Proxy Setting

```bash 
set http_proxy=http://127.0.0.1:10808
set http_proxys=http://127.0.0.1:10808
curl -v http://www.google.com



unset all_proxy

yum intall net-tool
```

### Linux 安装脚本
-- 以下转自 [https://www.v2ray.com/chapter_00/install.html]

V2Ray 提供了一个在 Linux 中的自动化安装脚本。这个脚本会自动检测有没有安装过 V2Ray，如果没有，则进行完整的安装和配置；如果之前安装过 V2Ray，则只更新 V2Ray 二进制程序而不更新配置。

以下指令假设已在 su 环境下，如果不是，请先运行 sudo su。

运行下面的指令下载并安装 V2Ray。当 yum 或 apt-get 可用的情况下，此脚本会自动安装 unzip 和 daemon。这两个组件是安装 V2Ray 的必要组件。如果你使用的系统不支持 yum 或 apt-get，请自行安装 unzip 和 daemon

### install 
bash <(curl -L -s https://install.direct/go.sh)
https://github.com/v2fly/fhs-install-v2ray/wiki/Migrate-from-the-old-script-to-this

此脚本会自动安装以下文件：

/usr/bin/v2ray/v2ray：V2Ray 程序；
/usr/bin/v2ray/v2ctl：V2Ray 工具；
/etc/v2ray/config.json：配置文件；
/usr/bin/v2ray/geoip.dat：IP 数据文件
/usr/bin/v2ray/geosite.dat：域名数据文件


此脚本会配置自动运行脚本。自动运行脚本会在系统重启之后，自动运行 V2Ray。目前自动运行脚本只支持带有 Systemd 的系统，以及 Debian / Ubuntu 全系列。

运行脚本位于系统的以下位置：

/etc/systemd/system/v2ray.service: Systemd
/etc/init.d/v2ray: SysV
脚本运行完成后，你需要：

编辑 /etc/v2ray/config.json 文件来配置你需要的代理方式；
运行 service v2ray start 来启动 V2Ray 进程；
之后可以使用 service v2ray start|stop|status|reload|restart|force-reload 控制 V2Ray 的运行。


### Config
https://github.com/v2fly/v2ray-examples

```json 
{
    "log": {
        "loglevel": "warning"
    },
    "routing": {
        "domainStrategy": "AsIs",
        "rules": [
            {
                "type": "field",
                "ip": [
                    "geoip:private"
                ],
                "outboundTag": "block"
            }
        ]
    },
    "inbounds": [
        {
            "listen": "0.0.0.0",
            "port": 58966,
            "protocol": "vmess",
            "settings": {
                "clients": [
                    {
                        "id": "b4175774-40dd-4493-9d1e-b43276dda55d"
                    }
                ]
            },
            "streamSettings": {
                "network": "tcp"
            }
        }
    ],
    "outbounds": [
        {
            "protocol": "freedom",
            "tag": "direct"
        },
        {
            "protocol": "blackhole",
            "tag": "block"
        }
    ]
}

```