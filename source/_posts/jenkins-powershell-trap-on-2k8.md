---
title: Jenkins迁移至Server2008执行Powershell无响应
date: 2017-12-22 10:41:31
tags:
---

**Windows Server 2K8的powershell版本默认为2.0。因版本过时，在使用过程中存在功能缺失、无法由Jenkins直接调用，cmd调用时exit code不更新%errorlevel%字段等问题，因此简直升级至最新版本根除隐患。**

相关链接：
[微软官方技术支持1](https://social.technet.microsoft.com/wiki/contents/articles/21016.how-to-install-windows-powershell-4-0.aspx)
[微软官方技术支持2](https://social.technet.microsoft.com/wiki/contents/articles/20623.step-by-step-upgrading-the-powershell-version-4-on-2008-r2.aspx)
简单来说：
1. [SP1-Windows 2008 R2](http://www.microsoft.com/en-us/download/details.aspx?id=5842)
1. [Microsoft .NET Framework 4.5.1) (Offline Installer)](http://www.microsoft.com/en-us/download/details.aspx?id=40779)
1. [Windows Management Framework 4.0](http://www.microsoft.com/en-us/download/details.aspx?id=40855)

安装后重启即可

------
**以下内容因未能从根源解决问题被标注过期供参考**

## 现象
将Jenkins从2k16迁移至2k8的服务器时，发生了jenkins pipeline中powershell脚本无响应的问题。
## 问题
* 无法通过下述方式直接执行powershell脚本。
``` groovy
powershell
'''
    #powershell scripts
'''
```
    由于系统原因，jenkins直接调用powershell，在执行过程中不会正常停止，也不会正常返回错误信息。
* Execution Policy
    虽然在服务器上已经更新了执行策略，但在jenkins调用时仍会提示策略为Restrict。
* x86 or x64
将使用的第三方包发布至*%WINDIR%\System32\WindowsPowerShell\v1.0\Modules*，执行Import-Module时找不到相关的包。

## 解决
* 将jenkinsfile文件中的powershell脚本迁出至**script.ps1**，通过**bat 'powershell -f script.ps1'**方式间接执行脚本。
* 在脚本执行前，添加**powershell Set-ExecutionPolicy Bypass**，提升jenkins调用的powershell环境的执行权限。
* 将第三方包分别添加至*%WINDIR%\System32\WindowsPowerShell\v1.0\Modules*、*%WINDIR%\SysWOW64\WindowsPowerShell\v1.0\Modules*中。确保第三方包可以被正常引用。
通过以上调整，问题解决。