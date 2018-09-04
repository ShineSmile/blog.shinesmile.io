---
title: Nuget命令行接口
date: 2017-12-28 18:53:24
tags: [nuget,cli]
---
翻译自：[NuGet CLI reference](https://docs.microsoft.com/en-us/nuget/tools/nuget-exe-cli-reference)
> 此官方文档过于拖沓，后续将结合实践重写。
# NuGet CLI 概述

Nuget命令行接口`nuget.exe`, 提供完整的及管理的功能，可以在在不修改任何工程文件的情况下对包进行安装、创建、 发布。

使用nuget命令只需在命令行窗体或bash shell中运行 `nuget <子命令> <可选参数>`即可，例如`nuget help pack`(显示pack文件的帮助信息)。

# 安装

1. 在Mac及Linux发行版中，安装[Mono 4.4.2及以上版本](http://www.mono-project.com/docs/getting-started/install/)。
2. 访问 [nuget.org/downloads](https://nuget.org/downloads)选择想使用的版本。
3. 下载链接为 `nuget.exe` 文件，保存至指定文件夹备用。此文件 *不是* 安装包; 在文件管理系统中双击执行不会得到任何结果。
4. 将放置 `nuget.exe` 的文件夹添加至PATH环境变量以便在其他位置方便的使用CLI调用它。

# 可用性

- Windows平台支持所有的命令。
- 除了明确指明的`pack`, `restore`, 及 `update`，All commands work with [nuget.exe running on Mono](../guides/install-nuget.md#mac-osx-and-linux) 
- Mac及Linux平台同样可以通过[dotnet CLI](dotnet-Commands.md) 调用`pack`、 `restore`、 `delete`、 `locals`、 和 `push` 命令。

# 命令及适用性

命令适用于包的创建、消费以及将其推送至托管服务器等情景：

| 普通命令 | 适用情景 | Nuget版本 | 描述 | 
| --- | --- | --- | --- |
| [pack](cli-ref-pack.md) | 创建 | 2.7+ |通过`.nuspec` 或工程文件创建nuget包。Mono下不支持通过工程文件创建。|
| [push](cli-ref-push.md) | 推送 | All | 将package推送至源服务器。 |
| [config](cli-ref-config.md) | 所有 | All | 读取或设置Nuget参数配置。 |
| [help or ?](cli-ref-help.md) | 所有 | All | 显示通用帮助或单条命令的帮助信息。 |
| [locals](cli-ref-locals.md) | 消费 | 3.3+ | 清理或列出列出包缓存地址或全局包 or the global packages folder, or identifies those folders. |
| [restore](cli-ref-restore.md) | Consumption | 2.7+ | Restores all packages referenced by the package reference format in use. When running on Mono, restoring packages using the PackageReference format is not supported. | 
| [setapikey](cli-ref-setapikey.md) | Publishing, Consumption | All | Saves an API key for a given package source when that package source requires a key for access. |
| [spec](cli-ref-spec.md) | Creation | All | Generates a `.nuspec` file, using tokens if generating the file from a Visual Studio project. |


| 二级命令 | 适用情景 | NuGet版本 | 描述 | 
| --- | --- | --- | --- |
| [add](cli-ref-add.md) | Publishing | 3.3+ | Adds a package to a non-HTTP package source using hierarchical layout. For HTTP sources, use *push*. |
| [delete](cli-ref-delete.md) | Publishing | All | Removes or unlists a package from a package source. |
| [init](cli-ref-init.md) | Creation | 3.3+ | Adds packages from a folder to a package source using hierarchical layout. |
| [install](cli-ref-install.md) | Consumption | All | Installs a package into the current project but does not modify projects or reference files. |
| [list](cli-ref-list.md) | Consumption, perhaps Publishing | All | Displays packages from a given source. |
| [mirror](cli-ref-mirror.md) | Publishing | Deprecated in 3.2+ | Mirrors a package and its dependencies from a source to a target repository. |
| [sources](cli-ref-sources.md) | Consumption, Publishing | All | Manages package sources in configuration files. |
| [update](cli-ref-update.md) | Consumption | All | Updates a project's packages to the latest available versions. Not supported when running on Mono. |

Different commands make use of various [Environment variables](cli-ref-environment-variables.md).

NuGet命令行接口情景分类:

| 情景 | 命令 |
| --- | --- |
| 消费 | `config`, `help`, `install`, `list`, `locals`, `restore`, `setapikey`, `sources`, `update` | 
| 创建 | `config`, `help`, `init`, `pack`, `spec` |
| 发布 | `add`, `config`, `delete`, `help`, `list`, `push`, `setapikey`, `sources` |

Developers concerned only with consuming packages, for example, need only understand that subset of NuGet commands.
