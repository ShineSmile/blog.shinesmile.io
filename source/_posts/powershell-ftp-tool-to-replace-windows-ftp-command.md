---
title: 使用powershell工具取代ftp.exe工具
date: 2017-12-19 10:18:26
tags: [powershell, ftp]
---
在使用Jenkins通过Windows*ftp.exe*进行持续发布的工作时受到很多限制：

* 不支持被动模式（PASV）,参考链接[stackoverflow](https://stackoverflow.com/questions/18643542/how-to-use-passive-ftp-mode-in-windows-command-prompt#19932879)。
    1. 使用此工具时，客户端必须开放防火墙供服务端“回访”。
    1. 当客户机处于NAT网络时，主动回访可能会被路由阻塞。
* 健壮性差，error pass silently
    1. 脚本只能按顺序执行
    1. 出错后无法停止
    1. 出现异常时无法针对异常调整流程
    1. 执行结束时返回值总是0，上传过程出现异常只能通过日志发现，无法显式被jenkins捕获。

可替代方案中：

* Putty提供的*psftp*工具无法正常连接。
* Winscp提供的*sftp*工具虽然可以提供被动模式，但是自动化执行的方式与*ftp.exe*类似：逐行执行文件中的ftp命令，[参考链接](https://winscp.net/eng/docs/guide_automation)。
* MS Web Deploy无法定制上传的内容。

偶然间发现了powershell下通过.NET框架库实现的[PSFTP](https://gallery.technet.microsoft.com/scriptcenter/PowerShell-FTP-Client-db6fe0cb)包，可以兼顾解决上述两个问题。

# 准备工作

1. 调整powershell脚本的执行策略，[Set-ExecutionPolicy](https://docs.microsoft.com/en-us/powershell/module/Microsoft.PowerShell.Security/Set-ExecutionPolicy?view=powershell-5.1)。

1. 下载[PowerShell FTP Client Module](https://gallery.technet.microsoft.com/scriptcenter/PowerShell-FTP-Client-db6fe0cb)。单用户使用请解压至*%USERPROFILE%\Documents\WindowsPowerShell\Modules*，全局路径在*%WINDIR%\System32\WindowsPowerShell\v1.0\Modules*。

在powershell中执行

``` powershell
Import-Module PSFTP
```

无异常则说明导入成功。

# 脚本概述

``` powershell
# 构建凭据对象
$user = "shine"
$Password =  ConvertTo-SecureString -String password -AsPlainText -Force
$credential = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList $user,$password
# 导入PSFTP包
Import-Module PSFTP
# 初始化FTP链接
Set-FTPConnection -Credentials $credential -Server ftp://10.20.30.40 -Session ftpSession -UsePassive
# 检查路径/path/to/bin/是否存在
try {
    $bin = Get-FTPChildItem -Session ftpSession -Path /path/to/bin/ -Recurse -Depth 2
}
catch {
    # 目标路径不存在，创建空目录
    Write-Output "Path /path/to/bin/ not exist,Try to build a new one ..."
    New-FTPItem -Session ftpSession -Name bin -Path /ac_user_api/
}
# 删除目标路径下的文件
if(![String]::IsNullOrEmpty($bin)){
    Write-Output "Cleaning up /path/to/bin/ ..."
    Remove-FTPItem -Session ftpSession -Path /path/to/bin/ -Recurse
    New-FTPItem -Session ftpSession -Name bin -Path /ac_user_api/
}
else{
    Write-Output "Folder /path/to/bin/ is Empty, skip clean up step."
}
# 遍历当前目录的文件，若为dll文件则上传至/path/to/bin/
cd bin
Get-ChildItem . | ForEach-Object -Process{
    if($_ -is [System.IO.FileInfo] -and $_.ToString().EndsWith(".dll"))
    {
        Send-FTPItem -Session ftpSession -Path /path/to/bin/ -LocalPath $_.name -Overwrite
    }
}
Write-Output "Upload Success!"
# 执行完毕，显式退出
exit 0
```