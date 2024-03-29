指令的一般格式为INSTRUCTION arguments，指令包括FROM、MAINTAINER、RUN等。

# 指令

- FROM

格式为FROM <image>或FROM <image>:<tag>。

第一条指令必须为FROM指令。并且，如果在同一个Dockerfile中创建多个镜像时，可以使用多个FROM指令（每个镜像一次）。

- MAINTAINER

格式为MAINTAINER <name>，指定维护者信息。

- RUN
格式为RUN <command>或RUN ["executable", "param1", "param2"]。

前者将在shell终端中运行命令，即/bin/sh -c；后者则使用exec执行。指定使用其它终端可以通过第二种方式实现，例如RUN ["/bin/bash", "-c", "echo hello"]。

每条RUN指令将在当前镜像基础上执行指定命令，并提交为新的镜像。当命令较长时可以使用\来换行。

- CMD
支持三种格式

CMD ["executable","param1","param2"]使用exec执行，推荐方式；
CMD command param1 param2在/bin/sh中执行，提供给需要交互的应用；
CMD ["param1","param2"]提供给ENTRYPOINT的默认参数；
指定启动容器时执行的命令，每个Dockerfile只能有一条CMD命令。如果指定了多条命令，只有最后一条会被执行。

如果用户启动容器时候指定了运行的命令，则会覆盖掉CMD指定的命令。

- EXPOSE
格式为EXPOSE <port> [<port>...]。

告诉Docker服务端容器暴露的端口号，供互联系统使用。

- ENV
格式为ENV <key> <value>。 指定一个环境变量，会被后续RUN指令使用，并在容器运行时保持。

例如

```
ENV PG_MAJOR 9.3
ENV PG_VERSION 9.3.4
RUN curl -SL http://example.com/postgres-$PG_VERSION.tar.xz | tar -xJC /usr/src/postgress && …
ENV PATH /usr/local/postgres-$PG_MAJOR/bin:$PATH
ADD
```
格式为ADD <src> <dest>。

该命令将复制指定的<src>到容器中的<dest>。 其中<src>可以是Dockerfile所在目录的一个相对路径；也可以是一个URL；还可以是一个tar文件（自动解压为目录）。则。

- COPY
格式为COPY <src> <dest>。

复制本地主机的<src>（为Dockerfile所在目录的相对路径）到容器中的<dest>。

当使用本地目录为源目录时，推荐使用COPY。

- ENTRYPOINT
两种格式：

ENTRYPOINT ["executable", "param1", "param2"]
ENTRYPOINT command param1 param2（shell中执行）。
配置容器启动后执行的命令，并且不可被docker run提供的参数覆盖。

每个Dockerfile中只能有一个ENTRYPOINT，当指定多个时，只有最后一个起效。

- VOLUME
格式为VOLUME ["/data"]。

创建一个可以从本地主机或其他容器挂载的挂载点，一般用来存放数据库和需要保持的数据等。

- USER
格式为USER daemon。

指定运行容器时的用户名或UID，后续的RUN也会使用指定用户。

当服务不需要管理员权限时，可以通过该命令指定运行用户。并且可以在之前创建所需要的用户，例如：RUN groupadd -r postgres && useradd -r -g postgres postgres。要临时获取管理员权限可以使用gosu，而不推荐sudo。

- WORKDIR
格式为WORKDIR /path/to/workdir。

为后续的RUN、CMD、ENTRYPOINT指令配置工作目录。

可以使用多个WORKDIR指令，后续命令如果参数是相对路径，则会基于之前命令指定的路径。例如

```
WORKDIR /a
WORKDIR b
WORKDIR c
RUN pwd
```
则最终路径为/a/b/c。

- ONBUILD
格式为ONBUILD [INSTRUCTION]。

配置当所创建的镜像作为其它新创建镜像的基础镜像时，所执行的操作指令。

例如，Dockerfile使用如下的内容创建了镜像image-A。

```
[...]
ONBUILD ADD . /app/src
ONBUILD RUN /usr/local/bin/python-build --dir /app/src
[...]
```
如果基于A创建新的镜像时，新的Dockerfile中使用FROM image-A指定基础镜像时，会自动执行ONBUILD指令内容，等价于在后面添加了两条指令。

```
    FROM image-A
 
#Automatically run the following
ADD . /app/src
RUN /usr/local/bin/python-build --dir /app/src
```
使用ONBUILD指令的镜像，推荐在标签中注明，例如php:7.1-fpm。


# 编译

docker build -t yee/php56 .

```
docker build -t user/name:version  -f Dockerfile [path]
-------------------------------------------------------------------------------
docker build： 用 Dockerfile 构建镜像的命令关键词。
[OPTIONS] : 命令选项，常用的指令包括 -t 指定镜像的名字，
    -f 显示指定构建镜像的 Dockerfile 文件（Dockerfile 可不在当前路径下），
     如果不使用 -f，则默认将上下文路径下的名为 Dockerfile 的文件认为是构建镜像的 "Dockerfile" 。
上下文路径|URL： 指定构建镜像的上下文的路径，构建镜像的过程中，可以且只可以引用上下文中的任何文件 。

```

# 查看


docker images

