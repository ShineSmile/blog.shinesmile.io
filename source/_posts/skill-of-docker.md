---
title: Docker使用技巧
date: 2017-03-23 11:08:52
tags:
---
# 背景

同[Ubuntu使用技巧](Ubuntu使用技巧)一样，这篇文章也是积累在学习docker中遇到的各种问题及解决方法（可能是临时）。

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

