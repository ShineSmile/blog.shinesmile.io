---
title: 使用U盘安装Windows Server 2016时的问题
date: 2017-12-12 13:04:38
tags: [windows,server]
---

## 文件大小超过4GB
Windows Server 2016的安装镜像中，`install.wim`文件大小为4.79 GB。使用UltraISO将server2016的ISO镜像刻录过程默认使用的是FAT32文件系统。此文件系统最大支持文件大小为4GB。因此在安装过程中会出现校验失败等问题。

## 解决方案
刻录完镜像后，可以使用下述命令将U盘的文件系统从FAT32转换为NTFS，解除单个文件大小的限制。
``` cmd
convert X: /fs:ntfs
```
其中`X`为目标磁盘的盘符。
执行此命令后，通过挂载光盘镜像，将`/sources/install.wim`文件拷贝到U盘对应的目录覆盖即可。

## 注意事项
* *convert*命令转换至NTFS文件系统是单向不可逆的转换！
* UEFI引导方式需要FAT32文件系统，使用此方法修改的U盘只能使用Legecy Boot方式启动。两种启动方式在引导、分区表、安全等方面存在差异，详情可参见[quora](https://www.quora.com/What-is-the-difference-between-UEFI-and-Legacy-Mode-which-we-need-to-choose-while-installing-the-OS)。
* 2017年12月11日：新版本的UltraISO在刻录Server 2016镜像时会尝试转换目标磁盘的文件系统并且失败。此问题可以通过更换烧录工具`PowerISO`来解决。
* 使用PowerISO烧录U盘前可以直接指定其文件系统。
* NTFS文件系统是针对机械硬盘而设计的，U盘在使用过程中可能会存在损坏U盘的问题。而exFAT是为闪存盘设计的文件系统，机械硬盘在使用时会产生丢失数据的现象。
* 未实际测试是否可以通过使用exFAT文件系统安装Server 2016。