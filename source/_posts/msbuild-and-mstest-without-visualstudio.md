---
title: .NET Framework CI环境配置
date: 2017-12-07 14:11:46
tags: [.net, Framework, MSBuild, MSTest]
---
条件允许的前提下，建议直接通过安装Visual Studio来获得整个构建、测试环境。
若硬盘空间紧张或其他条件导致不允许安装Visual Studio可参考下述方案进行配置：

# MSBuild

[MSBuild Tool 2017 下载地址](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=BuildTools&rel=15)

# Nuget

[下载地址](https://www.nuget.org/downloads)
MSBuild不包含nuget的命令行工具，因此需要手动下载备用。

# MSTest

[Visual Studio 2017 Test Agent 下载地址](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=TestAgent&rel=15)

# 注意事项

* MSBuild及MSTest均相当于Visual Studio 2017的新实例，不可安装在相同的路径，后安装的实例需要指定别称。![Visual Studio实例](MSBuild-and-MSTest-WITHOUT-Visual-Studio/visual_studio_instance.png)
* 使用Test Agent进行单元测试，需要向单元测试项目的引用路径添加单元测试相关的公共程序集路径。否则单元测试无法编译。![MSTest公共程序集](MSBuild-and-MSTest-WITHOUT-Visual-Studio/mstest_public_assembly.png)
