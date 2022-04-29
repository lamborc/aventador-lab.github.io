# Docker 常用技巧

## WSL 

> window 访问 WSL 文件 \\wsl$

```bash
explorer.exe .
code .
```

## WSL root 密码重置

- 关闭Ubuntu窗口
- 打开Powershell 或 cmd， 以root默认登陆 wsl -u root。
- 别关，在这个cmd窗口内（重点）输入 wsl 进入，
- 输入 passwd your_username ，确认密码。
- 关闭 WSL。 exit


## Docker MySQL8

```bash
docker pull mysql:8
docker images
```

### run mysql server

> docker run --name mysql8 -e MYSQL\_ROOT\_PASSWORD=123456 -d -p 3306:3306 mysql:8

```bash
docker exec -it mysql8 /bin/bash  # 进入容器

mysql -u root -p  # 

use mysql
select user,plugin from user;
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'root123';
ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY 'root123';
FLUSH PRIVILEGES;
```