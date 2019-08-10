# 命令
## 安装命令
1.安装docker-compose

```
apt install docker
apt install docker-compose
```

2.compose以守护进程模式运行加-d选项
```
$ docker-compose up -d
```
3.查看服务
```
docker-compose ps
```
4.查看compose日志
```
$ docker-compose logs web
$ docker-compose logs redis
```

5.停止compose服务
```
$ docker-compose stop
$ docker-compose ps
```

6.重启compose服务
```
$ docker-compose restart
$ docker-compose ps
```

7.kill compose服务
```
$ docker-compose kill
$ docker-compose ps
```

8.删除compose服务
```
$ docker-compose rm
```
9.更多的docker-compose命令可以使用
```
docker-compose --help
```
## compose命令

- version：指定 docker-compose.yml 文件的写法格式
- services：多个容器集合
- build：配置构建时，Compose 会利用它自动构建镜像，该值可以是一个路径，也可以是一个对象，用于指定 
```
Dockerfile 参数build: ./dir
---------------

build:
    context: ./mongo
    dockerfile: Dockerfile
    args:
        buildno: 1
```
- command：覆盖容器启动后默认执行的命令

```
command: bundle exec thin -p 3000
----------------------------------
command: [bundle,exec,thin,-p,3000]
```
- dns：配置 dns 服务器，可以是一个值或列表

```
dns: 8.8.8.8
------------
dns:
    - 8.8.8.8
    - 9.9.9.9
```
    
- dns_search：配置 DNS 搜索域，可以是一个值或列表

```
dns_search: example.com
------------------------
dns_search:
    - dc1.example.com
    - dc2.example.com
```
    
- environment：环境变量配置，可以用数组或字典两种方式

```
environment:
    MYSQL_USER: "root"
    MYSQL_PASSWORD: "123456"
    MYSQL_ROOT_PASSWORD: "123456"
    PMA_HOST: yee-mysql
```

- env_file：从文件中获取环境变量，可以指定一个文件路径或路径列表，其优先级低于 environment 指定的环境变量

```
env_file: .env
---------------
env_file:
    - ./common.env
```

- expose：暴露端口，只将端口暴露给连接的服务，而不暴露给主机
```
expose:
    - "3000"
    - "8000"
```

- image：指定服务所使用的镜像
```
image: phpmyadmin/phpmyadmin:latest

```
- network_mode：设置网络模式

```
network_mode: "bridge"
network_mode: "host"
network_mode: "none"
network_mode: "service:[service name]"
network_mode: "container:[container name/id]"
```

- networks

```
networks:
    extnetwork:    #自定义网络
    
        ipv4_address: 172.19.0.2
        ipam: 
            config: 
            - subnet: 172.19.0.0/16   #网段
            - gateway: 172.19.0.1
```

查看网络表：docker network ls
查看容器ip: cat /etc/hosts
```
root@ubuntu:~# docker exec -it yee-mysql bash
root@801c90d9d276:/# cat /etc/hosts
    127.0.0.1	localhost
    ::1	localhost ip6-localhost ip6-loopback
    fe00::0	ip6-localnet
    ff00::0	ip6-mcastprefix
    ff02::1	ip6-allnodes
    ff02::2	ip6-allrouters
    172.21.0.4	801c90d9d276
```

- ports：对外暴露的端口定义，和 expose 对应

```
ports:   # 暴露端口信息  - "宿主机端口:容器暴露端口"
- "8763:8763"
- "8763:8763"
```

- links：将指定容器连接到当前连接，可以设置别名，避免ip方式导致的容器重启动态改变的无法连接情况

```
links:    # 指定服务名称:别名 
    - docker-compose-eureka-server:compose-eureka
```
- volumes：卷挂载路径

```
volumes:
  - /lib
  - /var
```
  
- logs：日志输出信息
```
--no-color          单色输出，不显示其他颜.
-f, --follow        跟踪日志输出，就是可以实时查看日志
-t, --timestamps    显示时间戳
--tail              从日志的结尾显示，--tail=200
```

# 应用实战

```yaml
version: "2"
services:

redis:
    image: redis
    container_name: yee-redis
    ports:
    - "6379:6379"
    # volumes:
    networks:
        - net-redis
            
nginx:
    image: nginx
    container_name: yee-nginx
    ports:
        - "80:80"
        - "443:443"
    volumes:
        - ./conf/nginx/conf.d/:/etc/nginx/conf.d/:ro
        - ./conf/nginx/nginx.conf:/etc/nginx/nginx.conf:ro
        - ./log/nginx/:/var/log/nginx/:rw
        - ./www/:/var/www/:rw
    networks:
        - net-php

php:
    # build: ./php/php-fpm/ #dockerfile
    image: php:7.1-fpm
    container_name: yee-php71fpm
    expose:
        - "9000"
    volumes:
        - ./conf/php/php.ini:/etc/php/php.ini:ro
        - ./conf/php/php-fpm.conf:/etc/php/php-fpm.conf:ro
        - ./log/php/:/var/log/php/:rw
        - ./www/:/var/www/:rw
    networks:
        - net-php
        - net-mysql

mysql:
    image: yeechongyeung/m-mysql:5.7
    container_name: yee-mysql
    ports:
        - "3306:3306"
    volumes:
        - ./conf/mysql/my.cnf:/etc/mysql/my.cnf:ro
        - ./log/mysql/:/var/log/mysql/:rw
        - ./mysql/:/var/lib/mysql/:rw
    environment:
        MYSQL_USER: "root"
        MYSQL_PASSWORD: "123456"
        MYSQL_ROOT_PASSWORD: "123456"
    networks:
        - net-mysql

phpmyadmin:
    image: phpmyadmin/phpmyadmin:latest
    container_name: yee-phpmyadmin
    ports:
        - "8080:80"
    environment:
        MYSQL_USER: "root"
        MYSQL_PASSWORD: "123456"
        MYSQL_ROOT_PASSWORD: "123456"
        PMA_HOST: yee-mysql
    networks:
        - net-mysql

rtmpserver:
    image: yeechongyeung/m-nginx-rtmp:latest
    container_name: yee-rtmpserver
    ports:
        - "8090:8080"
        - "1945:1935"
    networks:
        - net-video

mongo:
    build:
        context: ./mongo
        dockerfile: Dockerfile
        container_name: yee-mongo
    restart: always
    ports:
        - "27017:27017"
    volumes:
        - ./mongo/db:/data/db:rw
        - ./mongo/localtime:/etc/localtime:rw

postgresql:
    image: kartoza/postgis:9.6-2.4
    container_name: yee-postgis
    ports:
        - "55433:5432"
    environment:
        POSTGRES_USER: postgres
        POSTGRES_PASS: 123456
        ALLOW_IP_RANGE: 0.0.0.0/0
    restart: always
    volumes:
        - ./postgresql:/var/lib/postgresql:rw
        - ./postgresql/tmp:/tmp/tmp:rw
    networks:
        - net-postgresql
        
networks:
        net-php:
        net-mysql:
        net-redis:
        net-video:
        net-postgresql:
        
```