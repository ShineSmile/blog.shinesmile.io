---
title: 在CentOS中安装Jenkins
date: 2017-12-03 14:42:27
tags: [jenkins, centos]
---
## 添加Jenkins源及相关证书（stable）
``` bash
sudo wget -O /etc/yum.repos.d/jenkins.repo http://pkg.jenkins-ci.org/redhat-stable/jenkins.repo
sudo rpm --import https://jenkins-ci.org/redhat/jenkins-ci.org.key
sudo yum install jenkins
```

## 使用OpenJDK8取代旧版本Java
``` bash
java -version
sudo yum remove java
sudo yum install java-1.8.0-openjdk
```
此处官方文档上使用的是`sudo yum install java-1.7.0-openjdk`，安装后jenkins无法正常访问，日志中显示了一个*java.lang.UnsupportedClassVersionError*，替换为1.8.0后服务可以正常运行。

## 添加、重载、查看防火墙规则
``` bash
sudo firewall-cmd --zone=public --add-port=8080/tcp --permanent
sudo firewall-cmd --zone=public --add-service=http --permanent
sudo firewall-cmd --reload
sudo firewall-cmd --list-all
```

## 配置jenkins服务

``` bash
sudo service jenkins start/stop/restart
sudo chkconfig jenkins on
```