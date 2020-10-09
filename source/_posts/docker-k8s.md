---
title: docker+k8s
date: 2020-09-12 11:18:15
tags:
---
# Docker+Kubernetes 部署

## Docker 基本概念

### 容器化技术介绍

- 对应用更快的进行标准化部署

### 容器化技术的应用场景

- 标准化的迁移方式
- 统一的参数配置
- 自动化部署
- 应用集群监控
- 开发与运维之间的沟通桥梁

### Docker 介绍

- 开源的应用容器引擎，基于 Go 语言开发
- 容器是完全使用沙箱机制，容器开销极低
- Docker 就是容器化技术的代名词（片面认为）
- Docker 也具备一定虚拟化职能
- 提供标准的应用打包方式

> [Docker](docker.com)

### 安装Docker(yum 安装方式)

1. 安装引用包

   ```shell
   yum install -y yum-utils device-mapper-persistent-data lvm2
   ```

2. 更换阿里云镜像安装源

   ```shel
   yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
   ```

   检查最快的安装源

   ```shel
   yum makecache fast
   ```

3. 安装 docker

   ```shell
   yum -y install docker-ce
   ```

   启动 docker

   ```shell
   service docker start
   ```

   查看 docker 版本

   ```sh
   docker version
   ```

4. 验证 docker 是否可以运行

   ```shell
   docker pull hello-world
   ```

   使用命令运行镜像

   ```shell
   docker run hello-world
   ```

5. 阿里云 Docker 镜像加速服务

   参考文档 [阿里云镜像加速服务](https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors)

### 容器与镜像

- **镜像**：镜像是文件，是只读的，提供了运行程序完整的软硬件资源，是应用程序的“集装箱”
- **容器**：是镜像的实例，由 Docker 负责创建，容器之间彼此隔离

### Docker 执行流程

由 Docker 客户端向 Docker 服务端发送命令

Docker 服务端包含了三个内容 

	1. Docker Daemon
 	2. Containers
 	3. Images

由远程仓库（registry）保存各种镜像文件

### Docker 常用命令
- docker pull 镜像名<:tags> - 从远程仓库抽取镜像
- docker images - 查看本地镜像
- docker run 镜像名<:tags> - 创建容器，启动应用
- docker ps - 查看正在运行中的镜像
- docker rm <-f> 容器id - 删除容器 -f为强制删除
- docker rmi <-f> 镜像名 <:tags> - 删除镜像

### Docker 快速部署 Tomcat
 1. 访问 hub.docker.com 寻找要找的镜像

 2. 下载 tomcat images   

  ```shell
 docker pull tomcat // 下载最新版本
 docker pull tomcat:8.5.46-jdk8-openjdk // 下载指定版本
  ```

 3.  启动 tomcat

  ```shell
 docker run tomcat 
  ```
### Docker 宿主机与容器通信

![docker宿主机与容器通信](D:\Image\Camera Roll\docker宿主机与容器通信.png)

使用宿主机和容器端口映射

```shell
docker run -p 8000:8080 tomcat 
```

- -p 代表着此容器映射端口

- 8000 为宿主机要映射的端口

- 8080 为容器的端口

- tomcat 指定要运行的images

docker-proxy 帮助我们转发 docker 端口

容器后台运行命令

```shell
docker run -d - p 8000:8080 tomcat
```

停止以及删除容器命令

```shell
docker stop container id // 停止容器
docker rm container id // 删除容器
docker rm -f container id // 强制移除容器
```

删除镜像

```shell
docker images // 查看镜像列表
docker rmi REPOSITORY:TAG // 删除镜像
// 如果该镜像正在使用中 可以先关闭容器 再删除镜像 也可以采用强制移除
docker rmi -f REPOSITORY:TAG // 强制移除镜像
```

#### 在容器中执行命令

- **格式**：docker exec [-it] 容器id 命令
- exec 在对容器中执行命令
- -it 采用交互方式执行命令
- **示例**：docker exec -it 5adeaf90c67e /bin/bash

#### Docker 默认存放路径

`/var/lib/docker`

镜像存储在 `image` 目录下

应用的容器存储在 `containers` 目录下

### Dockerfile 镜像描述文件
- Dockerfile 是一个包含用于组合镜像的命令的文本文档
- Docker 通过读取 Dockerfile 中的指令按步自动生成镜像
- docker build -t 机构/镜像名<:tags> Dockerfile 目录

#### Dockerfile 自动部署 Tomcat 应用
创建 Dockerfile 文件
- FROM tomcat:latest
- MAINTAINER liusk98.github.io
- WORKDIR /usr/local/tomcat/webapps
- ADD docker-web ./docker-web
再创建一个 docker-web 文件夹
使用 `docker build -t liusk98/mywebapp:1.0 .`
-t MAINTAINER<:tags> 
. 代表着在这个目录下 也可以使用绝对路径
再使用 `docker images` 命令便可以看到新创建的镜像
运行容器
	```shell
	docker run -d -p 8001:8080 liusk98/mywebapp:1.0
	```

#### Dockerfile 基础命令

##### FROM - 基于基准镜像

- FROM centos # 制作基准镜像(基于centos:lastest)
- FROM scratch #不依赖任何基准镜像 base image
- FROM tomcat: 9.0.22-jdk8-openjdk
- 尽量使用官方提供的 Base Image

##### LABEL & MAINTAINER - 说明信息

- MAINTAINER liusk98.github.io # 由哪个个人开发者或者机构 公司
- LABEL version = "1.0" # 关键信息使用 label 维护
- LABEL description = "蜀山斋" 

##### WORKDIR - 设置工作目录

- WORKDIR /usr/local
- WORKDIR /usr/local/newdir #自动创建
- 尽量使用绝对路径

##### ADD & COPY - 复制文件

- ADD hello / # 复制到根路径
- ADD test.tar.gz / # 添加根目录并解压
- ADD 除了复制，还具备添加远程文件功能

##### ENV - 设置环境常量

- ENV JAVA_HOME /usr/local/openjdk8
- RUN ${JAVA_HOME}/bin/java -jar test.jar
- 尽量使用环境常量，可提高程序维护性

#### Dockerfile 执行指令

##### RUN & CMD & ENTRYPOINT

- RUN : 在 Build 构建时执行命令
- ENTRYPOINT : 容器启动时执行的命令
- CMD : 容器启动后执行默认的命令或参数

##### 不同的执行时机

docker build 创建镜像 -- RUN 执行 在构建镜像时执行命令

docker run 创建容器 -- CMD|ENTRYPOINT 在容器创建时执行命令

##### RUN - 构建时运行

- RUN yum install -y vim # Shell 命令格式
- RUN ["yum", "install", "-y", "vim"] # Exec 命令格式

根本区别是否创建子进程，推荐使用 exec 格式

##### Shell 运行方式
- 使用 Shell 执行时，当前 shell 是父进程，生成一个子 shell 进程
- 在子 shell 中执行脚本。脚本执行完毕，退出子 shell，回到当前 shell。

##### Exec 运行方式

- 使用 Exec 方式，会用 Exec 进程替换当前进程，并且保持 PID 不变
- 执行完毕，直接退出，并不会退回之前的进程环境

##### ENTRYPOINT 启动命令

- ENTRYPOINT (入口点)用于在容器启动时执行命令
- Dockerfile 中只有最后一个 ENTRYPOINT 会被执行
- ENTRYPOINT ["ps"] # 推荐使用 Exec 格式

##### CMD 默认命令

- **CMD 外界可进行传参**

- CMD 用于设置默认执行的命令
- 如 Dockerfile 中出现多个 CMD，则只有最后一个被执行
- 如容器启动时附加指令，则 CMD 被忽略
- CMD ["ps", "-ef"] # 推荐使用 Exec 格式

### 镜像分层(layer)概念

1. 按层进行堆叠(分层)

2. 系统快照
   - 分层镜像可以进行复用

### Dockerfile - 构建 Redis 镜像

1. 进入 images 文件夹，创建新的存放 Dockerfile 的文件夹

   ```shell
   mkdir docker-reids
   cd docker-redis
   vim Dockerfile
   ```
   
2. 编辑 Dockerfile 文件 redis文件，配置文件 需要自己去官网下载
   ```vim
   FROM centos
   RUN ["yum", "install", "-y", "gcc", "gcc-c++", "net-tools", "make"]
   WORKDIR /usr/local
   ADD redis-4.0.14.tar.gz .
   WORKDIR /usr/local/redis-4.0.14/src
   RUN make && make install
   WORKDIR /usr/local/redis-4.0.14
   ADD redis-7000.conf .
   EXPOSE 7000
   CMD ["reids-server", "redis-7000.conf"]
   ```
   
3. 进行镜像构建

   ```shell
   docker build -t liusk98/docker_redis .
   ```

4. 创建容器

   ```shell
   docker run -p 7000:7000 liusk98.github.io/docker_redis
   ```

### 容器间的单向通信

创建容器 进行模拟通信

```shell
docker run -d --name web tomcat
docker run -d --name database -it centos /bin/bash
```

查看容器 IP

```shell
docker inspect CONTAINER ID
```

此时进入 tomcat 容器中和 database 的 IP 是可以 ping 通的

但是直接 ping database 的名字不可以，构建容器时需要进行 link 操作 

```shell
docker run -d --name web --link database tomcat
```

然后在进入这个容器 ping 一下 database 是通的

这就是单向通信

### Bridge 网桥双向通信

如果你在容器内部试图 ping 一下百度等联网地址，你会发现是可以 ping 通的，

因为网桥可以把从容器发出的数据包传输给外部的物理网卡，通过物理网卡与互联网交互

物理网卡得到的响应数据也会随着这个网桥返回给容器内部，网桥在这里就充当了 Docker 环境与外侧宿主机的通信员。

查看 docker 网络服务的命令

```shell
docker network ls
```

如果我们要实现某些容器互联互通的话需要创建一个新的网桥

```shell
docker network create -d bridge my-bridge
```

通过新的网桥建立连接

```shell
docker network connect my-bridge web 容器名称
docker network connect my-bridge database
```

 进入 database 的容器查看是否连接成功

```shell
docker exec -it 容器ID /bin/bash
ping web
```

再用同样的方法查看 tomcat 是否可以 ping 通

在 docker 中每当创建一个网桥就会在宿主机中创建一个虚拟网卡

这个虚拟网卡也承担了一个网关的作用

我们的虚拟容器通过虚拟网卡就可以在容器内部实现互联互通

### Volume 容器间共享数据

> Volume 数据卷

#### 通过设置 -v 挂载宿主机目录

格式：

​    docker run --name 容器名 -v 宿主机路径: 容器内挂载路径 镜像名

实力: 

​    docker run --name t1 -v /usr/webapps:/usr/local/tomcat/webapps tomcat

#### 通过 --volumes -from 共享容器内挂载点

- 创建共享容器

  ```shell
  docker create --name webpage -v /webapps:/tomcat/webapps tomcat /bin/true
  ```
- 共享容器挂载点

  ```shell
docker run --volumes-from webpage --name t1 -d tomcat
  ```

准备挂载

```shell
docker run --name t1 -p 8000:8080 -d -v /usr/webapps:/usr/local/tomcat/webapps tomcat
```

完成运行之后可在本地测试能够打开 tomcat 页面

