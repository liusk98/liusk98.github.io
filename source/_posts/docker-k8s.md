---
title: docker+k8s
date: 2020-09-12 11:18:15
tags:
---
# Docker+Kubernetes ����

## Docker ��������

### ��������������

- ��Ӧ�ø���Ľ��б�׼������

### ������������Ӧ�ó���

- ��׼����Ǩ�Ʒ�ʽ
- ͳһ�Ĳ�������
- �Զ�������
- Ӧ�ü�Ⱥ���
- ��������ά֮��Ĺ�ͨ����

### Docker ����

- ��Դ��Ӧ���������棬���� Go ���Կ���
- ��������ȫʹ��ɳ����ƣ�������������
- Docker ���������������Ĵ����ʣ�Ƭ����Ϊ��
- Docker Ҳ�߱�һ�����⻯ְ��
- �ṩ��׼��Ӧ�ô����ʽ

> [Docker](docker.com)

### ��װDocker(yum ��װ��ʽ)

1. ��װ���ð�

   ```shell
   yum install -y yum-utils device-mapper-persistent-data lvm2
   ```

2. ���������ƾ���װԴ

   ```shel
   yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
   ```

   ������İ�װԴ

   ```shel
   yum makecache fast
   ```

3. ��װ docker

   ```shell
   yum -y install docker-ce
   ```

   ���� docker

   ```shell
   service docker start
   ```

   �鿴 docker �汾

   ```sh
   docker version
   ```

4. ��֤ docker �Ƿ��������

   ```shell
   docker pull hello-world
   ```

   ʹ���������о���

   ```shell
   docker run hello-world
   ```

5. ������ Docker ������ٷ���

   �ο��ĵ� [�����ƾ�����ٷ���](https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors)

### �����뾵��

- **����**���������ļ�����ֻ���ģ��ṩ�����г�����������Ӳ����Դ����Ӧ�ó���ġ���װ�䡱
- **����**���Ǿ����ʵ������ Docker ���𴴽�������֮��˴˸���

### Docker ִ������

�� Docker �ͻ����� Docker ����˷�������

Docker ����˰�������������

	1. Docker Daemon
 	2. Containers
 	3. Images

��Զ�ֿ̲⣨registry��������־����ļ�

### Docker ��������
- docker pull ������<:tags> - ��Զ�ֿ̲��ȡ����
- docker images - �鿴���ؾ���
- docker run ������<:tags> - ��������������Ӧ��
- docker ps - �鿴���������еľ���
- docker rm <-f> ����id - ɾ������ -fΪǿ��ɾ��
- docker rmi <-f> ������ <:tags> - ɾ������

### Docker ���ٲ��� Tomcat
 1. ���� hub.docker.com Ѱ��Ҫ�ҵľ���

 2. ���� tomcat images

  ```shell
 docker pull tomcat // �������°汾
 docker pull tomcat:8.5.46-jdk8-openjdk // ����ָ���汾
  ```

 3.  ���� tomcat

  ```shell
 docker run tomcat
  ```
### Docker ������������ͨ��

![docker������������ͨ��](D:\Image\Camera Roll\docker������������ͨ��.png)

ʹ���������������˿�ӳ��

```shell
docker run -p 8000:8080 tomcat
```

- -p �����Ŵ�����ӳ��˿�

- 8000 Ϊ������Ҫӳ��Ķ˿�

- 8080 Ϊ�����Ķ˿�

- tomcat ָ��Ҫ���е�images

docker-proxy ��������ת�� docker �˿�

������̨��������

```shell
docker run -d - p 8000:8080 tomcat
```

ֹͣ�Լ�ɾ����������

```shell
docker stop container id // ֹͣ����
docker rm container id // ɾ������
docker rm -f container id // ǿ���Ƴ�����
```

ɾ������

```shell
docker images // �鿴�����б�
docker rmi REPOSITORY:TAG // ɾ������
// ����þ�������ʹ���� �����ȹر����� ��ɾ������ Ҳ���Բ���ǿ���Ƴ�
docker rmi -f REPOSITORY:TAG // ǿ���Ƴ�����
```

#### ��������ִ������

- **��ʽ**��docker exec [-it] ����id ����
- exec �ڶ�������ִ������
- -it ���ý�����ʽִ������
- **ʾ��**��docker exec -it 5adeaf90c67e /bin/bash

#### Docker Ĭ�ϴ��·��

`/var/lib/docker`

����洢�� `image` Ŀ¼��

Ӧ�õ������洢�� `containers` Ŀ¼��

### Dockerfile ���������ļ�
- Dockerfile ��һ������������Ͼ����������ı��ĵ�
- Docker ͨ����ȡ Dockerfile �е�ָ����Զ����ɾ���
- docker build -t ����/������<:tags> Dockerfile Ŀ¼

#### Dockerfile �Զ����� Tomcat Ӧ��
���� Dockerfile �ļ�
- FROM tomcat:latest
- MAINTAINER liusk98.github.io
- WORKDIR /usr/local/tomcat/webapps
- ADD docker-web ./docker-web
�ٴ���һ�� docker-web �ļ���
ʹ�� `docker build -t liusk98/mywebapp:1.0 .`
-t MAINTAINER<:tags>
. �����������Ŀ¼�� Ҳ����ʹ�þ���·��
��ʹ�� `docker images` �������Կ����´����ľ���
��������
	```shell
	docker run -d -p 8001:8080 liusk98/mywebapp:1.0
	```

#### Dockerfile ��������

##### FROM - ���ڻ�׼����

- FROM centos # ������׼����(����centos:lastest)
- FROM scratch #�������κλ�׼���� base image
- FROM tomcat: 9.0.22-jdk8-openjdk
- ����ʹ�ùٷ��ṩ�� Base Image

##### LABEL & MAINTAINER - ˵����Ϣ

- MAINTAINER liusk98.github.io # ���ĸ����˿����߻��߻��� ��˾
- LABEL version = "1.0" # �ؼ���Ϣʹ�� label ά��
- LABEL description = "��ɽի"

##### WORKDIR - ���ù���Ŀ¼

- WORKDIR /usr/local
- WORKDIR /usr/local/newdir #�Զ�����
- ����ʹ�þ���·��

##### ADD & COPY - �����ļ�

- ADD hello / # ���Ƶ���·��
- ADD test.tar.gz / # ��Ӹ�Ŀ¼����ѹ
- ADD ���˸��ƣ����߱����Զ���ļ�����

##### ENV - ���û�������

- ENV JAVA_HOME /usr/local/openjdk8
- RUN ${JAVA_HOME}/bin/java -jar test.jar
- ����ʹ�û�������������߳���ά����

#### Dockerfile ִ��ָ��

##### RUN & CMD & ENTRYPOINT

- RUN : �� Build ����ʱִ������
- ENTRYPOINT : ��������ʱִ�е�����
- CMD : ����������ִ��Ĭ�ϵ���������

##### ��ͬ��ִ��ʱ��

docker build �������� -- RUN ִ�� �ڹ�������ʱִ������

docker run �������� -- CMD|ENTRYPOINT ����������ʱִ������

##### RUN - ����ʱ����

- RUN yum install -y vim # Shell �����ʽ
- RUN ["yum", "install", "-y", "vim"] # Exec �����ʽ

���������Ƿ񴴽��ӽ��̣��Ƽ�ʹ�� exec ��ʽ

##### Shell ���з�ʽ
- ʹ�� Shell ִ��ʱ����ǰ shell �Ǹ����̣�����һ���� shell ����
- ���� shell ��ִ�нű����ű�ִ����ϣ��˳��� shell���ص���ǰ shell��

##### Exec ���з�ʽ

- ʹ�� Exec ��ʽ������ Exec �����滻��ǰ���̣����ұ��� PID ����
- ִ����ϣ�ֱ���˳����������˻�֮ǰ�Ľ��̻���

##### ENTRYPOINT ��������

- ENTRYPOINT (��ڵ�)��������������ʱִ������
- Dockerfile ��ֻ�����һ�� ENTRYPOINT �ᱻִ��
- ENTRYPOINT ["ps"] # �Ƽ�ʹ�� Exec ��ʽ

##### CMD Ĭ������

- **CMD ���ɽ��д���**

- CMD ��������Ĭ��ִ�е�����
- �� Dockerfile �г��ֶ�� CMD����ֻ�����һ����ִ��
- ����������ʱ����ָ��� CMD ������
- CMD ["ps", "-ef"] # �Ƽ�ʹ�� Exec ��ʽ

### ����ֲ�(layer)����

1. ������жѵ�(�ֲ�)

2. ϵͳ����
   - �ֲ㾵����Խ��и���

### Dockerfile - ���� Redis ����

1. ���� images �ļ��У������µĴ�� Dockerfile ���ļ���

   ```shell
   mkdir docker-reids
   cd docker-redis
   vim Dockerfile
   ```

2. �༭ Dockerfile �ļ� redis�ļ��������ļ� ��Ҫ�Լ�ȥ��������
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

3. ���о��񹹽�

   ```shell
   docker build -t liusk98/docker_redis .
   ```

4. ��������

   ```shell
   docker run -p 7000:7000 liusk98.github.io/docker_redis
   ```

### ������ĵ���ͨ��

�������� ����ģ��ͨ��

```shell
docker run -d --name web tomcat
docker run -d --name database -it centos /bin/bash
```

�鿴���� IP

```shell
docker inspect CONTAINER ID
```

��ʱ���� tomcat �����к� database �� IP �ǿ��� ping ͨ��

����ֱ�� ping database �����ֲ����ԣ���������ʱ��Ҫ���� link ����

```shell
docker run -d --name web --link database tomcat
```

Ȼ���ڽ���������� ping һ�� database ��ͨ��

����ǵ���ͨ��

### Bridge ����˫��ͨ��

������������ڲ���ͼ ping һ�°ٶȵ�������ַ����ᷢ���ǿ��� ping ͨ�ģ�

��Ϊ���ſ��԰Ѵ��������������ݰ�������ⲿ������������ͨ�����������뻥��������

���������õ�����Ӧ����Ҳ������������ŷ��ظ������ڲ�������������ͳ䵱�� Docker �����������������ͨ��Ա��

�鿴 docker ������������

```shell
docker network ls
```

�������Ҫʵ��ĳЩ����������ͨ�Ļ���Ҫ����һ���µ�����

```shell
docker network create -d bridge my-bridge
```

ͨ���µ����Ž�������

```shell
docker network connect my-bridge web ��������
docker network connect my-bridge database
```

 ���� database �������鿴�Ƿ����ӳɹ�

```shell
docker exec -it ����ID /bin/bash
ping web
```

����ͬ���ķ����鿴 tomcat �Ƿ���� ping ͨ

�� docker ��ÿ������һ�����žͻ����������д���һ����������

�����������Ҳ�е���һ�����ص�����

���ǵ���������ͨ�����������Ϳ����������ڲ�ʵ�ֻ�����ͨ

### Volume �����乲������

> Volume ���ݾ�

#### ͨ������ -v ����������Ŀ¼

��ʽ��

    docker run --name ������ -v ������·��: �����ڹ���·�� ������

ʵ��:

    docker run --name t1 -v /usr/webapps:/usr/local/tomcat/webapps tomcat

#### ͨ�� --volumes -from ���������ڹ��ص�

- ������������

  ```shell
  docker create --name webpage -v /webapps:/tomcat/webapps tomcat /bin/true
  ```
- �����������ص�

  ```shell
docker run --volumes-from webpage --name t1 -d tomcat
  ```

׼������

```shell
docker run --name t1 -p 8000:8080 -d -v /usr/webapps:/usr/local/tomcat/webapps tomcat
```

�������֮����ڱ��ز����ܹ��� tomcat ҳ��

###
