---
title: 轻量级Nuget服务的搭建
date: 2017-12-29 15:57:55
tags: [nuget, server]
---

本文将通过Nuget.Server的Nuget包建立轻量级的Nuget服务。

1. 在Visual Studio中建立新的空Web应用：

依次点选 文件 | 新建 | 工程 目录选项 (或直接使用CTRL + SHIFT + N) 打开新建工程对话框，如下图选择ASP.NET空Web应用。
![new empty Web Application](http://nugetserver.net/gfx/New-Project-dialog-box.png)
![project created preview](http://nugetserver.net/gfx/New-project-in-Solution-Explorer.png)

1. 安装Nuget.Server包：

通过Nuget管理工具添加Nuget.Server包。
![add Nuget.Server package to project](http://nugetserver.net/gfx/NuGet.Server-package.png)

1. 配置文件夹所在路径：

从`Nuget.Server 1.5`起，可以通过配置修改保存包的路径。`web.config`文件包括名为`packagesPath`新的`appSetting`节点，当key未指定或值为空时，包路径为`~/Packages`。值可以为**绝对路径**或**相对路径**。

``` xml
<appSettings>
    <add key="packagesPath" value="C:\MyPackages" />
</appSettings>
```

1. 将软件包放置在packages文件夹下

一切就绪，Nuget.Server将空web应用转换能够托管OData种子的网站。在Packages文件夹中添加的package会被展示出来。
自NuGet.Server 1.4起，可以通过**Nuget.exe**添加轻量级的种子。web.config文件appSetting节点下有名为apiKey的节点，当此键未指定或
为空值时，推送功能将被禁止。设置apiKey的值将启用**Nuget.exe**的推送功能。

``` xml
<appSettings>
    <add key="requireApiKey" value="true" />
    <add key="apiKey" value="some-guid" />
</appSettings>
```

如果服务足够安全，或者不想进行校验，可以将`requireApiKey`的值设置为false。
5. 发布并运行

* 调试预览：

![debug preview](http://nugetserver.net/gfx/Package-feed-home-page.png)

* 查看包列表

![view packages list](http://nugetserver.net/gfx/OData-over-ATOM-package-feed.png)

* 在Visual Studio中添加新的源

![add to sources](http://nugetserver.net/gfx/Adding-new-feed-to-package-sources.png)