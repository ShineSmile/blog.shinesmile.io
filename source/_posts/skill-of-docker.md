---
title: Docker使用技巧
date: 2017-03-23 11:08:52
tags:
---
# 背景

同[Ubuntu使用技巧](http://heavensfeel.net/2017/03/22/skill-of-ubuntu/)一样，这篇文章也是积累在学习docker中遇到的各种问题及解决方法（可能是临时）。

docker和git有很多相似之处，有一定的git基础对理解docker里的一些概念会有很大帮助。

操作系统为：**Ubuntu 16.04.2 LTS**

Docker版本：

``` plain
Client:
 Version:      17.03.0-ce
 API version:  1.26
 Go version:   go1.7.5
 Git commit:   3a232c8
 Built:        Tue Feb 28 08:01:32 2017
 OS/Arch:      linux/amd64

Server:
 Version:      17.03.0-ce
 API version:  1.26 (minimum version 1.12)
 Go version:   go1.7.5
 Git commit:   3a232c8
 Built:        Tue Feb 28 08:01:32 2017
 OS/Arch:      linux/amd64
 Experimental: false
```

## 概述

* 两端：客户端和服务端
* 三核心：镜像、容器、仓库

## 执行docker命令时不使用sudo

> docker daemon使用的是Unix socket而不是TCP端口，而属于root的Unix socket在其他用户调用时必须通过sudo来提升用户的权限。[摘自](https://docs.docker.com/engine/installation/linux/linux-postinstall/)

解决方案是添加一个docker用户组，并为当前用户添加此次要用户组。

``` bash
sudo groupadd docker
sudo usermod -aG docker $USER
```

执行上述命令后可能需要注销重新登录重新加载用户配置使配置生效。

## 设置Docker daemon的代理服务器

修改docker的推荐方案是修改`/etc/docker.daemon.json`配置文件，详细配置参看[这里](https://docs.docker.com/engine/reference/commandline/dockerd//#daemon-configuration-file)

这里选择的方法是修改deamon的配置项:

``` bash
mkdir -p /etc/systemd/system/docker.service.d
/etc/systemd/system/docker.service.d/http-proxy.conf
```

内容为：

``` plain
[Service]
Environment="HTTP_PROXY=http://proxy.example.com:80/"
```

重载服务参数：

``` bash
sudo systemctl daemon-reload
```

验证：

``` bash
systemctl show --property=Environment docker
```

重启服务：

``` bash
sudo systemctl restart docker
```

## docker 子命令

### 镜像相关

* pull 拉取镜像至本地
* search 查询远端仓库的共享镜像
* rmi remove image 删除镜像
* commit 创建镜像
* images 查看本地镜像
* commit 提交新的镜像至本地
* save 将镜像存储为tar包
* load 载入tar包中的镜像
* push 推送镜像

### 容器相关

* create 通过容器创建镜像
* start 运行制定容器
* stop 停止容器
* kill 强制停止容器
* run 获取镜像，通过镜像创建容器并运行
* rm 删除容器
* export 导出容器
* import 导入容器

### 仓库相关

获取第三方镜像仓库的方法：在镜像前添加仓库的url，如：

``` bash
sudo docker pull dl.dockerpool.com:5000/ubuntu:12.04
```

## docker run参数简要说明

* -t 让Docker分配一个伪终端（pseudo-tty）并绑定到容器的标准输入上
* -i 让容器的标准输入保持打开
* -d 让容器以守护态在后台运行
* -p 开放容器的端口并映射至本机：`本机端口:容器端口`。
* -v 映射目标卷宗至本地：`本机路径:容器路径`

## 无法通过映射的端口访问容器的端口

这个问题很可能不是docker自身导致的，首先要核实服务建立时绑定的IP地址是否是`127.0.0.1`或者`localhost`，如果是，尝试将地址更换为`0.0.0.0`再试。

这可能是我见过最简单无情的坑。

## 通过Remote API访问其他主机中的Docker Daemon

如果不想在工作中的电脑运行Docker Deamon服务，在开发过程中直接调用服务端的容器，可以开启服务器daemon的tcp访问。**此方法不具有授权及身份认证功能。请自行核对docker及daemon的版本号。**

``` bash
# 查看配置文件位于哪里
systemctl show --property=FragmentPath docker
# 编辑配置文件内容，接收所有ip请求
sudo vim /lib/systemd/system/docker.service
```

注释目标行，并追加:

``` plain
#ExecStart=/usr/bin/dockerd -H fd://
ExecStart=/usr/bin/dockerd -H unix:///var/run/docker.sock -H tcp://0.0.0.0:2375
```

``` bash
# 重新加载配置文件，重启docker daemon
sudo systemctl daemon-reload
sudo systemctl restart docker
# 测试
curl http://localhost:2375/info
# 开启防火墙
sudo ufw allow 2375/tcp
```

本地计算机修改用户环境变量：

``` bash
# IP地址为服务器地址
export DOCKER_HOST="tcp://192.168.1.100:2375"
# 测试
docker ps
docker version
```

衍生自：[Docker Remote API 如何使用？](https://www.zhihu.com/question/24852884/answer/138069968)
