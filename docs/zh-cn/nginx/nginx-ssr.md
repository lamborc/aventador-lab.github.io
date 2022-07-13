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
firewall-cmd --zone=public --add-port=37000-39999/tcp --permanent
firewall-cmd --zone=public --add-port=37000-39999/udp --permanent
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
    <title>Welcome My Blog</title>
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
ln -s /var/lib/snapd/snap /snap # 创建链接（snap软件包一般安装在/snap目录下）非常重要
sudo snap install core     # 
sudo snap refresh core     # 
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
```