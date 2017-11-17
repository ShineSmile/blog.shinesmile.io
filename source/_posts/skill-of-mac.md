---
title: Mac使用技巧
date: 2017-03-23 19:19:47
tags: mac
---

这篇文章主要用于记录日常使用Mac的过程中遇到的问题和解决方案。

## 环境变量的设置

### 修改个人PATH

用户登录时会执行`~/.bash_profile`中的内容，若想简单修改个人的环境变量，可以通过向此文件添加bash命令对环境变量进行调整。

### 修改本机PATH

此方法相对友好，仔细观察`~/.bash_profile`文件包括以下内容：

``` bash
eval $(/usr/libexec/path_helper -s)
```

`/usr/libexec/path_helper -s`的执行结果就是我们的PATH内容并将其export。

显然上述输出并不是直接写在单个文件中，而是在`/etc/paths.d/`目录下，此文件夹可能已有部分文件，用户登录时，上述命令会对文件的内容进行遍历并生成指令，将其添加至PATH中。

### 修改env环境变量

1. `/etc/profile` 全局（公有）配置，不管是哪个用户，登录时都会读取该文件。不建议修改该文件。
1. `/etc/bashrc` 全局（公有）配置，bash shell执行时，不管是何种方式，都会读取此文件。可以通过此文件修改全局变量。
1. `~/.bash_profile` 每个用户都可使用该文件输入专用于自己使用的shell信息,当用户登录时,该文件仅仅执行一次!可以通过此文件修改个人环境变量。

### 使上述配置文件立即生效

``` bash
source ~/.bash_profile
```

### 查询最大线程数
```bash
sysctl kern.num_threadsd
sysctl kern.num_taskthreads
```