---
title: Powershell第三方包管理工具PsGet
date: 2017-12-28 14:46:04
tags: 
---
本文翻译自[Github/PsGet/Readme.md](https://github.com/psget/psget)

PsGet实用工具集
=============

通过中央目录、本地文件及网络安装Poweshell模块的命令集。

安装
============

在Powershell中执行:

	(new-object Net.WebClient).DownloadString("http://psget.net/GetPsGet.ps1") | iex

如果返回信息为:

	Downloading PsGet from https://github.com/psget/psget/raw/master/PsGet/PsGet.psm1
	PsGet is installed and ready to use

表示安装成功. PowerShell脚本通过下载 `GetPsGet.ps1` 重定向至 `Invoke-Expression` 来安装PsGet模块。

或者手动进行安装：

1. 复制 `PsGet.psm1` 至模块文件夹 (例如： `$Env:PsGet\PsGet\` )
2. 执行 `Import-Module PsGet` (或者添加至profile中)
3. 搞定!

特性
========

1. 通过中央目录、本地文件或网络安装模块。
2. 向用户及本地profile中添加自导入模块 (需要提升权限)
3. 通过Zip文件安装模块
4. 安装后导入模块
5. 存在于profile中的模块将在PowerShell启动时自动导入
6. 执行Install.ps1
7. 支持模块的Tab键补全,例如： ismo Ps<Tab>

示例
========
通过中央目录安装模块仅需键入:

    install-module PsUrl
    
这条命令会通过中央目录查询PsUrl的依赖信息，并在找到后进行安装。

另一种[安装 `PsUrl` 模块](https://github.com/chaliy/psurl/raw/master/PsUrl/PsUrl.psm1)的方法为：

    install-module -ModuleUrl https://github.com/chaliy/psurl/raw/master/PsUrl/PsUrl.psm1
    
像 `posh-git` 这样的压缩文件, 可以通过 [zip package](https://github.com/dahlbyk/posh-git/zipball/master) 安装

    install-module -ModuleUrl https://github.com/dahlbyk/posh-git/zipball/master
    
这条命令将执行  `posh-git` 的安装脚本 `Install.ps1` . (中央目录已收录`posh-git`, 因此 `install-module posh-git` 就够了。)

像ZIP及PSM1这样的本地文件也同样没问题:

    install-module -ModulePath \TestModules\HelloWorld.zip
    install-module -ModulePath \TestModules\HelloWorld.psm1
    
将模块添加至Profile中使其在加载时自执行:

    install-module PsUrl -Startup   

Nuget也同样可以用来安装模块:

    install-module -nugetpackageid SomePowerShellModuleOnNuget
    install-module -nugetpackageid SomePrivatePowerShellModule -nugetsource http://mynugetserver/nuget/feed/

如果需要对模块升级, 执行 `Update-Module` 将现有版本更新至最新版：

    update-module PsUrl

常见问题
===

Q: 错误 `File [x] cannot be loaded because the execution of scripts is disabled on this system. Please see "get-help about_signing" for more details.`
A: 出于安全的考虑，Powershell默认会限制所有脚本的执行， “修正”方法为, 使用管理员身份启动powershell并执行 
    
    Set-ExecutionPolicy RemoteSigned
    
获取更多详情, 执行 `get-help` [关于执行策略](http://msdn.microsoft.com/en-us/library/dd347641.aspx).

Q: 如何将我自己的模块添加到中央目录?
A: 查看[wiki](https://github.com/psget/psget/wiki)中的[这部分](https://github.com/psget/psget/wiki/How-to-add-your-module-to-the-directory)内容。


Roadmap、资源、贡献等略
=======