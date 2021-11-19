# Let's Encrypt Cert  
> 

## Certbot 申请aliyun 域名证书

> 环境:Centos 7 & python3 

```bash
yum install python36
```

> apply cert

```bash
/data/certbot/venv/bin/certbot certonly \
-a dns-aliyun \
--dns-aliyun-credentials /data/certbot/aliyun.ini \
--dns-aliyun-propagation-seconds 60 \
-d domain.cc
-d *.cc
``` 

> Aliyun AccessAPI conf

```text
dns_aliyun_access_key = xxx
dns_aliyun_access_key_secret = xxx
``` 

> auto renew python 
```python
""" certbot auto renew script """
import random;
import time;

time.sleep(random.random() * 9000)

```

> 定时任务 
crontab -l 列出所有任务
我们用crontab -e进入当前用户的工作表编辑

```text
* * * * * 分别对应 分,时,日,月,周 五种

3,15 8-11 */2 **  # 代表每隔两天的上午8点到11点的第3分钟 和 15 分钟执行
```





echo "0 0,17 * * * root python3 -c 'import random; import time; time.sleep(random.random() * 3600)' && /data/certbot/venv/bin/certbot renew -q" | sudo tee -a /etc/crontab > /dev/null

## Steps  

> 配置定时任务执行 renew auto cert

Centos7 
> service : /etc/systemd/system/certauo-renew.service

```service
[Unit]
Description=Auto renew SSL for certbot

[Service]
Type=simple
ExecStart=/data/certbot/cert-auto-renew.sh

[Install]
WantedBy=multi-user.target
```

```bash

systemctl enable certauto-renew
```

> renew shell : 

```bash
#!/bin/bash
curTs=$(date "+%Y-%m-%d %H:%M:%S")
echo "renew at : ${curTs}" >  /data/certbot/renew-excu.log
nohup /data/certbot/venv/bin/certbot renew  > output >>  /data/certbot/renew-excu.log
---- 

### ISSUE 

#### Centos 7 openssl.conf file Direction

> Centos 7.9 openssl.conf at /etc/pki/tls/openssl.conf


  