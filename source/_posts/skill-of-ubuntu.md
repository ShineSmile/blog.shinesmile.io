---
title: Ubuntu使用技巧
date: 2017-03-22 20:14:31
tags: ubuntu
---

说起来惭愧，Linux零零散散使用了两年多，都是现用现查，除了几个基础的文件操作命令，各种配置、服务的设置什么都没记住。在OneNote中的笔记基本都经历着写-用不到-删的循环，删过之后就全忘了。既然这次打算好好折腾一下docker和go，干脆把这篇文章写起来，不求全，但会把自己在使用Ubuntu过程中遇到的问题尽可能的记下来，给自己列个F&Q，不能证明自己会的多，只能说自己曾经折腾过。

版本为：**Ubuntu 16.04.2 LTS**

## 网络地址的配置

安装的时候选择了DHCP作为地址的分配方式，由于安装后需要使用SSH，所以需要分配一个固定的IP地址。

首先使用`ifconfig`查看各个网卡的状态，确定目前正在使用的网卡。打开`/etc/network/interfaces`文件，编辑格式如下：

``` conf
iface eth0 inet static
address 172.16.14.130
gateway 172.16.14.1
netmask 255.255.0.0
```

使用`sudo /etc/init.d/networking restart`来重启网络服务，执行后没有效果，直接重启服务器加载解决此问题。

## DNS服务器

修改`/etc/resolvconf/resolv.conf.d/base`，格式为：

``` conf
nameserver 8.8.8.8
nameserver 8.8.4.4
```

重载方式：`sudo resolvconf -u`，之后可以通过`cat /etc/resolv.conf`查看配置是否生效。

## 修改网络代理

通过设置ShadowSocks开的HTTP代理，提高更新和安装包时的效率。需要修改的配置文件是`/etc/apt/apt.conf`，内容格式大致为：

`Acquire::http::Proxy “http://yourproxyaddress:proxyport”;`

另一种是修改`http_proxy`环境变量`export http_proxy="http://用户名:密码@代理IP:代理端口"`

测试方法为`curl ip.gs`，查看返回的结果。

## 使用apt-get install无法更新的包

`0 upgraded, 0 newly installed, 0 to remove and 9 not upgraded.`

使用`sudo apt-get dist-upgrade`解决。[dist-upgrade installs new kernels and such it does not install a newer release of Ubuntu.](http://askubuntu.com/questions/352635/0-upgraded-0-newly-installed-0-to-remove-and-428-not-upgraded)

## 查看Ubuntu版本

`cat /etc/issue`

## 防火墙管理

``` bash
sudo apt-get install ufw #安装ubuntu firewall
sudo ufw allow 2375/tcp #仅允许TCP方式访问5000端口
sudo ufw allow 5000 #允许外部允许5000 tcp/udp方式访问
sudo ufw status #查看防火墙状态及端口号
sudo ufw delete 3 #删除第三条ufw规则
```

## perl: warning: Setting locale failed

``` bash
echo "export LC_ALL=C" >> ~/.bashrc
source ~/.bashrc
```

## 设置API及相关命令的代理服务器

 如果只是希望apt-get使用代理，可以在终端下编辑`/etc/apt/apt.conf`加入下面这行，
```
Acquire::http::Proxy "http://yourproxyaddress:proxyport";
```
然后保存退出。

## 挂起正在执行的任务

* 挂起任务的快捷键为*Ctrl+Z*
* 使用*bg %jobnumber*将从后台执行指定任务
* 使用*fg %jobnumber*将任务切换回前台执行
* 使用*jobs*查询挂起的任务

## 查询线程数
``` bash
cat /proc/sys/kernel/pid_max #进程数
cat /proc/sys/kernel/threads-max #线程数
ulimit -u #用户进程数
```

## modify locale
``` bash
sudo dpkg-reconfigure locales
```

## 对mosh进行本地化兼容
``` bash
localedef -v -c -i en_US -f UTF-8 en_US.UTF-8
```