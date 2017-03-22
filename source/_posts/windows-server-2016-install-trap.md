---
title: 使用U盘安装Windows Server 2016时的问题
date: 2017-03-22 13:04:38
tags:
---
# 背景

Windows Server 2016的安装镜像中，`install.wim`文件大小为4.79 GB。使用UltraISO将server2016的ISO镜像刻录过程使用的是FAT32文件系统。此文件系统最大支持文件大小为4GB。因此在安装过程中会出现校验失败等问题。

## 方案

刻录完镜像后，可以使用下述命令将U盘的文件系统从FAT32转换为NTFS，解除单个文件大小的限制。

``` cmd
convert X: /fs:ntfs
```

其中`X`为目标磁盘的盘符。

执行此命令后，通过挂载光盘镜像，将`/sources/install.wim`文件拷贝到U盘对应的目录覆盖即可。

## 注意事项

UEFI引导方式需要FAT32文件系统，使用此方法修改的U盘只能使用Legecy Boot方式启动。