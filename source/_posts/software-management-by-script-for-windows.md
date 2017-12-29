---
title: 使用Chocolate通过脚本管理Windows应用
date: 2017-12-28 14:45:40
tags: [powershell, choco]
---

在类Unix系统中，我们可以使用例如port、apt-get、yum此类包管理工具，对应用进行快速便捷的安装。在Windows平台，通过chololate，我们同样可以做到通过一条命令完成应用及依赖项的安装。

## 依赖项
Windows 7+ / Windows Server 2003+
PowerShell v2+
.NET Framework 4+ (若未安装安装过程会尝试自动安装.Net 4.0)

## 安装

### 使用cmd安装：

在具有管理员权限的cmd.exe中执行：
``` 
@"%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe" -NoProfile -InputFormat None -ExecutionPolicy Bypass -Command "iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))" && SET "PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin"
```

### 使用powershell安装：

在具有管理员权限的powershell中执行：
``` powershell
Set-ExecutionPolicy Bypass -Scope Process -Force; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))
```

执行完毕且没有错误提示的情况下表示安装成功。

## choco子命令

choco包裹以下子命令
 * list - 列出远程或本地的包
 * search - 搜索远程或本地的包 (list的别名)
 * info - 查询软件包的摘要信息
 * install - 通过各个源安装软件包
 * pin - 强制更新软件包
 * outdated - 查询过期的包. 
 * upgrade - 通过各种源升级软件包
 * uninstall - 卸载软件包
 * pack - 打包nuspec至nupkg
 * push - 推送编译好的nupkg
 * new - 从模板中为chocolate软件包生成必要的文件
 * source - 显示及配置默认的源
 * sources - source的别名
 * config - 查看及设置配置文件
 * feature - 查看及设置特性
 * features - feature的别名
 * apikey - 为指定的源查询或设置apikey
 * setapikey - apikey的别名
 * unpackself - have chocolatey set itself up
 * version - [不推荐] 将在v1版移除，使用 `choco outdated` 或 `cup <pkg|all> -whatif` 代替
 * update - [不推荐] 为未来版本保留此命令