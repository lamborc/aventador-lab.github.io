# Nginx 配置子目录网站

## Docker Nginx 配置
> Docker nginx 安装

```bash
docker pull nginx  # download
docker images #  查看拉去镜像
docker run --name nginx -p 80:80 -d nginx # 
docker run --name nginx -p 80:80 -v e:\docker\nginx\www:/usr/share/nginx  -v e:\docker\nginx\conf:/etc/nginx/conf.d -d nginx

```

- 命令：docker run -d -p 8088:80 --name nginx-server nginx  解释
docker run：运行镜像
-d：设置容器在后台一直运行
-p：端口进行映射，将本地 8088 端口映射到容器内部的 80 端口
–name nginx-server：设置一个名字
nginx：就是docker images中的REPOSITORY，也可以设置为docker images中的IMAGE ID



## 多子目录配置


## 反向代理配置


