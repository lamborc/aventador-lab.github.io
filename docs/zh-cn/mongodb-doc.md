# MongoDB
https://wylu.me/posts/7043b2c8/


### MongoDB 换端口开启远程

- 修改配置 /etc/mongodb.conf port & bind_ip:0.0.0.0
- 重启MongoDB
- 增加root用户 db.createUser({user: 'root', pwd: 'root', roles: ['root']})
- 修改配置 auth = true 注释掉#noauth = true

#### change port 

```configuration
net:
  port: 27717
  bindIp: 0.0.0.0
```

修改


```configuration
net:
  port: 27717
  bindIp: 0.0.0.0
```