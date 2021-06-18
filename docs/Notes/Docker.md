# Docker

## 1. 学习内容

- Docker 概述
- Docker 安装
- Docker 命令
- Docker 镜像
- 容器数据卷
- DockerFile
- Docker 网络原理
- IDEA 整合 Docker
- Docker Compose
- Docker Swarm
- CI\CD Jenkins

## 2. Docker概述

### 2.1 Docker 的基本组成

![查看源图像](https://geekflare.com/wp-content/uploads/2019/09/docker-architecture-609x270.png)

**镜像（image）**

**容器（container）**

**仓库（repository）**

### 2.2 安装 Docker

> 环境准备

1、Linux

2、CentOS 7

3、Xshell

> 环境查看

```shell
# 系统内核 3.10 以上
[root@iZbp11rhvd25b9dwktym67Z /]# uname -r
3.10.0-957.21.3.el7.x86_64
```

```shell
# 系统版本
[root@iZbp11rhvd25b9dwktym67Z /]# cat /etc/os-release
NAME="CentOS Linux"
VERSION="7 (Core)"
ID="centos"
ID_LIKE="rhel fedora"
VERSION_ID="7"
PRETTY_NAME="CentOS Linux 7 (Core)"
ANSI_COLOR="0;31"
CPE_NAME="cpe:/o:centos:centos:7"
HOME_URL="https://www.centos.org/"
BUG_REPORT_URL="https://bugs.centos.org/"

CENTOS_MANTISBT_PROJECT="CentOS-7"
CENTOS_MANTISBT_PROJECT_VERSION="7"
REDHAT_SUPPORT_PRODUCT="centos"
REDHAT_SUPPORT_PRODUCT_VERSION="7"
```

> 安装

```shell
# 1、卸载旧版本
yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
                  
# 2、需要的安装包
yum install -y yum-utils
# 3、设置镜像仓库
yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo # 默认国外
    
    #换成阿里云
yum-config-manager \
--add-repo \
https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
# 更新yum软件包索引
[root@iZbp11rhvd25b9dwktym67Z /]# yum makecache fast
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
base                                                      | 3.6 kB  00:00:00     
docker-ce-stable                                          | 3.5 kB  00:00:00     
epel                                                      | 4.7 kB  00:00:00     
extras                                                    | 2.9 kB  00:00:00     
updates                                                   | 2.9 kB  00:00:00     
(1/2): docker-ce-stable/7/x86_64/updateinfo               |   55 B  00:00:00     
(2/2): docker-ce-stable/7/x86_64/primary_db               |  62 kB  00:00:00     
Metadata Cache Created
# 4、安装docker相关，docker-ce社区版 ee企业版
yum install docker-ce docker-ce-cli containerd.io
# 5、启动docker
systemctl start docker
[root@iZbp11rhvd25b9dwktym67Z /]# systemctl start docker
# 6、docker version 确认安装成功
[root@iZbp11rhvd25b9dwktym67Z /]# docker version
Client: Docker Engine - Community
 Version:           20.10.7
 API version:       1.41
 Go version:        go1.13.15
 Git commit:        f0df350
 Built:             Wed Jun  2 11:58:10 2021
 OS/Arch:           linux/amd64
 Context:           default
 Experimental:      true

Server: Docker Engine - Community
 Engine:
  Version:          20.10.7
  API version:      1.41 (minimum version 1.12)
  Go version:       go1.13.15
  Git commit:       b0f5bc3
  Built:            Wed Jun  2 11:56:35 2021
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          1.4.6
  GitCommit:        d71fcd7d8303cbf684402823e425e9dd2e99285d
 runc:
  Version:          1.0.0-rc95
  GitCommit:        b9ee9c6314599f1b4a7f497e1f1f856fe433d3b7
 docker-init:
  Version:          0.19.0
  GitCommit:        de40ad0

# 7、hello-world
[root@iZbp11rhvd25b9dwktym67Z /]# docker run hello-world
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
b8dfde127a29: Pull complete 
Digest: sha256:9f6ad537c5132bcce57f7a0a20e317228d382c3cd61edae14650eec68b2b345c
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/

# 8、查看卸载的 hello-world镜像
[root@iZbp11rhvd25b9dwktym67Z /]# docker images
REPOSITORY    TAG       IMAGE ID       CREATED        SIZE
hello-world   latest    d1165f221234   3 months ago   13.3kB
```

> 卸载docker

```
yum remove docker-ce docker-ce-cli containerd.io

sudo rm -rf /var/lib/docker  
sudo rm -rf /var/lib/containerd
```

### 2.3 阿里云镜像加速

1、登录阿里云找到容器服务

![](https://gitee.com/sun-qiao321/picture/raw/master/images/20210617095247.png)

2、找到镜像加速地址

![](https://gitee.com/sun-qiao321/picture/raw/master/images/20210617095432.png)

3、配置使用

```shell
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://zdvucwcn.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```

### 2.4 底层原理



## 3. Docker命令

### 3.1 帮助命令

```shell
docker version  #显示docker版本信息
docker info     #显示docker的系统信息
docker 命令 --help
```

文档 [Reference documentation | Docker Documentation](https://docs.docker.com/reference/)

### 3.2 镜像命令

**docker images**

**docker search**

**docker pull**

```shell
# 安装mysql 
[root@iZbp11rhvd25b9dwktym67Z /]# docker pull mysql:5.7
5.7: Pulling from library/mysql
69692152171a: Pull complete 
1651b0be3df3: Pull complete 
951da7386bc8: Pull complete 
0f86c95aa242: Pull complete 
37ba2d8bd4fe: Pull complete 
6d278bb05e94: Pull complete 
497efbd93a3e: Pull complete 
a023ae82eef5: Pull complete 
e76c35f20ee7: Pull complete 
e887524d2ef9: Pull complete 
ccb65627e1c3: Pull complete 
Digest: sha256:a682e3c78fc5bd941e9db080b4796c75f69a28a8cad65677c23f7a9f18ba21fa
Status: Downloaded newer image for mysql:5.7
docker.io/library/mysql:5.7

```

**docker rmi**

### 3.3 容器命令

说明：有了镜像才可以创建容器

**docker run**

**docker ps**

**exit**

**ctrl + p + q**

**docker rm**

**docker start**

**docker restart**

**docker stop**

**docker kill**

### 3.4 Docker 安装 Nginx

### 3.5 Docker 安装 Tomcat

1、下载镜像

docker pull tomcat:9.0

2、启动运行

docker run -d -p 8080:8080 --name tomcat01 tomcat:9.0

查看开启的容器

```shell
[root@iZbp11rhvd25b9dwktym67Z ~]# docker ps
CONTAINER ID   IMAGE        COMMAND             CREATED       STATUS       PORTS                    NAMES
8949c64dbbaa   tomcat:9.0   "catalina.sh run"   3 hours ago   Up 3 hours   0.0.0.0:8080->8080/tcp   tomcat01

```

3、防火墙设置

firewall-cmd --state 查看防火墙状态

```shell
[root@iZbp11rhvd25b9dwktym67Z ~]# firewall-cmd --state
not running
```

此时防火墙关闭。

systemctl start firewalld 启动防火墙

```shell
[root@iZbp11rhvd25b9dwktym67Z ~]# systemctl start firewalld
[root@iZbp11rhvd25b9dwktym67Z ~]# firewall-cmd --state
running
```

systemctl enable firewalld.service 将防火墙设置为开机自启动

```shell
[root@iZbp11rhvd25b9dwktym67Z ~]# systemctl enable firewalld.service
Created symlink from /etc/systemd/system/dbus-org.fedoraproject.FirewallD1.service to /usr/lib/systemd/system/firewalld.service.
Created symlink from /etc/systemd/system/multi-user.target.wants/firewalld.service to /usr/lib/systemd/system/firewalld.service.
```

netstat -ntlp 查看当前所有tcp端口

```shell
[root@iZbp11rhvd25b9dwktym67Z ~]# netstat -ntlp
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
tcp        0      0 0.0.0.0:8080            0.0.0.0:*               LISTEN      28439/docker-proxy  
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      1073/sshd  
```

firewall-cmd --zone=public --add-port=8080/tcp --permanent 开放8080端口

```shell
[root@iZbp11rhvd25b9dwktym67Z ~]# firewall-cmd --zone=public --add-port=8080/tcp --permanent
success

```

控制台添加规则

![](https://gitee.com/sun-qiao321/picture/raw/master/images/20210617143406.png)

firewall-cmd --reload 重启防火墙

```shell
[root@iZbp11rhvd25b9dwktym67Z ~]# firewall-cmd --reload
success

```

重新访问地址

![](https://gitee.com/sun-qiao321/picture/raw/master/images/20210617143806.png)

此时访问不了是因为docker下载的阿里云tomcat镜像只有最小可运行环境。

4、进入容器

docker exec -it tomcat01 /bin/bash

发现webapps里没有东西

```shell
[root@iZbp11rhvd25b9dwktym67Z ~]# docker exec -it tomcat01 /bin/bash
root@8949c64dbbaa:/usr/local/tomcat# ls
BUILDING.txt	 NOTICE		RUNNING.txt  lib	     temp	   work
CONTRIBUTING.md  README.md	bin	     logs	     webapps
LICENSE		 RELEASE-NOTES	conf	     native-jni-lib  webapps.dist
root@8949c64dbbaa:/usr/local/tomcat# cd webapps
root@8949c64dbbaa:/usr/local/tomcat/webapps# ls

```

将webapps.dist中文件拷如 webapps

```shell
root@8949c64dbbaa:/usr/local/tomcat# cp -r webapps.dist/* webapps
root@8949c64dbbaa:/usr/local/tomcat# cd webapps
root@8949c64dbbaa:/usr/local/tomcat/webapps# ls
ROOT  docs  examples  host-manager  manager

```

刷新界面，访问成功！！

![](https://gitee.com/sun-qiao321/picture/raw/master/images/20210617144901.png)

### 3.6 Docker 安装 MySQL

1、拉取镜像

```shell
[root@iZbp11rhvd25b9dwktym67Z /]# docker pull mysql:5.7
5.7: Pulling from library/mysql
69692152171a: Pull complete 
1651b0be3df3: Pull complete 
951da7386bc8: Pull complete 
0f86c95aa242: Pull complete 
37ba2d8bd4fe: Pull complete 
6d278bb05e94: Pull complete 
497efbd93a3e: Pull complete 
a023ae82eef5: Pull complete 
e76c35f20ee7: Pull complete 
e887524d2ef9: Pull complete 
ccb65627e1c3: Pull complete 
Digest: sha256:a682e3c78fc5bd941e9db080b4796c75f69a28a8cad65677c23f7a9f18ba21fa
Status: Downloaded newer image for mysql:5.7
docker.io/library/mysql:5.7
```

2、创建容器

docker run --name mysql01 -p 3306:3306 -e MYSQL_ROOT_PASSWORD=123456 -d mysql:5.7

出现错误：

```shell
[root@iZbp11rhvd25b9dwktym67Z ~]# docker run --name mysql01 -p 3306:3306 -e MYSQL_ROOT_PASSWORD=123456 -d mysql:5.7
docker: Error response from daemon: Conflict. The container name "/mysql01" is already in use by container "dfb09249e63a9e78e9508bae3059f7b8e37f072b85d161c5f26d9cfe4016d838". You have to remove (or rename) that container to be able to reuse that name.
See 'docker run --help'.
```

删除该id docker rm dfb09249e63a9e78e9508bae3059f7b8e37f072b85d161c5f26d9cfe4016d838

重启docker 服务器  systemctl restart docker

再次创建

```shell
[root@iZbp11rhvd25b9dwktym67Z ~]# docker run --name mysql01 -p 3306:3306 -e MYSQL_ROOT_PASSWORD=123456 -d mysql:5.7
ae0a5dbe57422ab963d2e56aaf3715a6b88a48612037cc53ad1c998af3041893
[root@iZbp11rhvd25b9dwktym67Z ~]# docker ps
CONTAINER ID   IMAGE       COMMAND                  CREATED         STATUS         PORTS                               NAMES
ae0a5dbe5742   mysql:5.7   "docker-entrypoint.s…"   5 seconds ago   Up 4 seconds   0.0.0.0:3306->3306/tcp, 33060/tcp   mysql01
```

3、先查看防火墙

netstat -ntlp

开启3306端口

4、进入容器

docker exec -it ae0a5dbe5742 /bin/bash

5、登录mysql

```shell
root@ae0a5dbe5742:/# mysql -uroot -p
Enter password: 
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 2
Server version: 5.7.34 MySQL Community Server (GPL)

Copyright (c) 2000, 2021, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

```

6、执行命令

```shell
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
4 rows in set (0.00 sec)

```

7、退出

exit

exit

