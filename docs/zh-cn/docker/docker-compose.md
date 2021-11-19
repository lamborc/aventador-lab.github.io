# Docker Compose 配置文件

> yml 配置文件

```yml
version:'2'
service:
  web:
  	image:dockercloud/hello-img
  	ports:
  	  - 8800
  	networks:
  	  - front-tier
  	  - back-tier
  redis:
  	image: redis
  	links:
  	  - web
  	networks:
  	  - back-tier

  lb:
  	image: dockercloud/haproxy
  	ports:
  	  - 80:80
  	links:
  	  - web
  	networks:
  	  - front-tier
  	  - back-tier
  	volumes:
  	  - /var/run/docker.sock:/var/run/docker.sock

networks:
  front-tier:
  	driver: bridge
  back-tier:
    driver: bridge

```

> 一份标准配置文件应该包含 version、services、networks 三大部分,其中最关键的就是 services 和 networks 两个部分

### Sevice 配置部分


```yml
service:
# 定义服务名字
  web:  
# 镜像名称或ID,docker 本地不存在就会到Compose 拉去,  image: ubuntu:14.04  
  	image: redis   

```

#### Image

> image 则是指定服务的镜像名称或镜像 ID。如果镜像在本地不存在，Compose 将会尝试拉取这个镜像

  - image: redis
  - image: ubuntu:14.04
  - image: tutum/influxdb
  - image: example-registry.com:4000/postgresql
  - image: a4bc65fd

#### build

> 服务除了可以基于指定的镜像，还可以基于一份 Dockerfile，在使用 up 启动之时执行构建任务，这个构建标签就是 build，它可以指定 Dockerfile 所在文件夹的路径。Compose 将会利用它自动构建这个镜像，然后使用这个镜像启动服务容器

```yml
build: /path/to/build/dir
build: ./dir
# 设定上下文根目录，然后以该目录为准指定 Dockerfile。
build:
  context: ../
  dockerfile: path/of/Dockerfile
```

**注意**
>>如果你同时指定了 image 和 build 两个标签，那么 Compose 会构建镜像并且把镜像命名为 image 后面的那个名字

#### arg 标签

```yml
build:
  context: .
  args:
    buildno: 1
    password: secret
```

> 下面这种写法也是支持的，一般来说下面的写法更适合阅读

```yml
build:
  context: .
  args:
    - buildno=1
    - password=secret
```

> YAML 的布尔值（true, false, yes, no, on, off）必须要使用引号引起来（单引号、双引号均可），否则会当成字符串解析。

#### depends_on

> 在使用 Compose 时，最大的好处就是少打启动命令，但是一般项目容器启动的顺序是有要求的，如果直接从上到下启动容器，必然会因为容器依赖问题而启动失败
> 例如在没启动数据库容器的时候启动了应用容器，这时候应用容器会因为找不到数据库而退出，为了避免这种情况我们需要加入一个标签，就是 depends_on，这个标签解决了容器的依赖、启动先后的问题。

```yml
version: '2'
services:
  web:
    build: .
    depends_on:
      - db
      - redis
  redis:
    image: redis
  db:
    image: postgres
```

#### links

> 上面的depends_on吧，那个标签解决的是启动顺序问题，这个标签解决的是容器连接问题，与Docker client的--link一样效果，会连接到其它服务中的容器

#### ports

> 映射端口的标签。使用HOST:CONTAINER格式或者只是指定容器的端口，宿主机会随机映射端口

__当使用HOST:CONTAINER格式来映射端口时，如果你使用的容器端口小于60你可能会得到错误得结果，因为YAML将会解析xx:yy这种数字格式为60进制。所以建议采用字符串格式。__

#### volumes

> 挂载一个目录或者一个已存在的数据卷容器，可以直接使用 [HOST:CONTAINER] 这样的格式，或者使用 [HOST:CONTAINER:ro] 这样的格式，后者对于容器来说，数据卷是只读的，这样可以有效保护宿主机的文件系统

> Compose的数据卷指定路径可以是相对路径，使用 . 或者 .. 来指定相对目录

```yml
volumes:
  // 只是指定一个路径，Docker 会自动在创建一个数据卷（这个路径是容器内部的）。
  - /var/lib/mysql

  // 使用绝对路径挂载数据卷
  - /opt/data:/var/lib/mysql

  // 以 Compose 配置文件为中心的相对路径作为数据卷挂载到容器。
  - ./cache:/tmp/cache

  // 使用用户的相对路径（~/ 表示的目录是 /home/<用户目录>/ 或者 /root/）。
  - ~/configs:/etc/configs/:ro

  // 已经存在的命名的数据卷。
  - datavolume:/var/lib/mysql
```

#### networks

> 加入指定网络，格式如下

```yml
services:
  some-service:
    networks:
     - some-network
     - other-network
```