### docker + nginx + php-fpm + mysql + mongo + phpmyadmin + nginx-rtmp + postgresql

# 使用
----

1. 安装 `docker` 和 `docker-compose`
```
apt install docker
apt install docker-compose
```
2. git clone 代码到本地
    ```
    $ git clone https://github.com/yeechongyeung/Docker-Web-Onestep.git
    ```
3. 执行命令
    ```
    $ cd Docker-Web-Env
    $ docker-compose up -d
    ```
4. 默认站点在浏览器中访问 `localhost`

   phpmyadmin 访问 `localhost:8080`, 帐号 `root` 密码 `root`

# 目录结构
----

```
├── conf                         配置目录
│   ├── mysql                    MySQL配置文件目录
│   │   └── my.cnf               MySQL配置文件
│   ├── nginx                    Nginx配置文件目录
│   │   ├── conf.d               站点配置文件目录
│   │   │   └── default.conf     默认站点配置文件
│   │   └── nginx.conf           Nginx通用配置文件
│   └── php                      PHP配置目录
│       │── php.ini              PHP配置文件
│       └── php-fpm.conf         PHP-FPM配置文件
├── log                          日志目录
│   ├── mysql                    MySQL日志目录
│   ├── nginx                    Nginx日志目录
│   └── php                      PHP日志目录
├── mysql                        MySQL数据文件目录
├── php                          PHP目录
│   └── php56                    PHP5.6版本
│       └── Dockerfile           Dockerfile配置文件
│   └── php-fpm                  7.1-fpm
│       └── Dockerfile           Dockerfile配置文件
├── mongo                        mongo目录
│   └── Dockerfile               Dockerfile配置文件
│   └── data                     录入数据目录
│       └── ****.json            要录入的数据
│   └── db                       数据库文件
│   └── localtime                localtime
├── postgresql                   postgresql目录
│   └── 9.6                      数据库数据目录
│   └── tmp                      临时目录
├── www                          站点根目录
│   └── index.php                index文件
└── docker-compose.yml           docker-compose配置文件
```
## postgresql

数据库工具箱：pgadmin4

配置：
host: host IP
port:55433
database:postgres
username:postgres
password:123456
