---
title: 使用Jenkins对.NET Framework工程进行持续集成
date: 2017-12-04 16:17:46
tags: [jenkins, dotnet, ci, msbuild]
---
## 安装Jenkins
本文档直接使用Windows平台上的Jenkins完成.NET Framework平台的持续集成相关工作。[Windows平台Jenkins下载地址](https://jenkins.io/download/thank-you-downloading-windows-installer-stable)，解压后双击安装MSI软件包即可成功安装服务。服务的默认端口为8080，首次登陆时jenkins会要求在服务端指定路径找到秘钥文件对所有权进行验证。
## MSBuild
> The Microsoft Build Engine is a platform for building applications. This engine, which is also known as MSBuild, provides an XML schema for a project file that controls how the build platform processes and builds software. Visual Studio uses MSBuild, but it doesn't depend on Visual Studio. By invoking msbuild.exe on your project or solution file, you can orchestrate and build products in environments where Visual Studio isn't installed.
>微软生成引擎，即MSBuild，是应用程序的构建平台。MSbuild提供了一种用来描述工程的XML纲要，用来声明使用的编译工具及编译过程。MSBuild不依赖于Visual Studio，相反Visual Studio需要MSBuild才能工作。在未安装Visual Studio的环境中，通过调用MSBuild.exe并指明具体的工程或解决方案文件，即可对产品进行编译及构建。
>——摘自[Microsoft Offical Document](https://docs.microsoft.com/en-us/visualstudio/msbuild/msbuild)
MSBuild现已开源，代码在[Github](https://github.com/Microsoft/msbuild)进行托管。
### MSBuild的版本
早期的MSbuild的通常随.NET SDK一同安装在在全局程序集缓存（Global Assembly Cache）下，其路径为:`C:\Windows\Microsoft.NET\Framework\<version>\MSBuild.exe`。
![不同版本.NET Framwork使用的MSBuild版本](jenkins-for-dotnet-framework/msbuild_legacy_version.png)
新版的MSBuild版本号为15.4.8.50001，随dotnet core SDK其路径变更为：`C:\Program Files (x86)\Microsoft Visual Studio\2017\Enterprise\MSBuild`
![MSBuild ver.15.4.8](jenkins-for-dotnet-framework/msbuid_v15.png)
### 常用参数
|Switch|Short form|Description|  
|------------|----------------|-----------------|  
|/help|/? or /h|显示可用的帮助信息. 例:<br /> `msbuild.exe /?`|
|/nologo||不显示MSBuild的启动信息|
|/maxcpucount[:`number`]|/m[:`number`]|显示构建时同时使用的CPU个数的最大值。不附加此标识时默认值为1，附加此标识且不指定数目时，MSBuild将尽可能多的使用CPU核心运行。更多信息请参阅[并行编译多个工程](https://docs.microsoft.com/en-us/visualstudio/msbuild/building-multiple-projects-in-parallel-with-msbuild).<br />下面的例子表示MSBuild将同时使用三个进程实现三个工程同时编译的效果。<br /> `msbuild myproject.proj /maxcpucount:3`|
|/property:`name`=`value`|/p:`name`=`value`|设置或重写工程级别的属性，其形式为`name`:`value`键值对。多个键值对需要分开声明，或者使用分号或者逗号隔开。例如:<br /> `/property:WarningLevel=2;OutDir=bin\Debug`|  
|/toolsversion:`version`|/tv:`version`|用来制定构建构成时使用的工具集版本。例如: `/toolsversion:3.5`<br /><br /> 特别注意：工具集版本号不与工程的目标平台不能等同而视，目标平台版本指工程构建后运行的.NET Framework平台。Note: The toolset version isn’t the same as the target framework, which is the version of the .NET Framework on which a project is built to run. |  

## 准备工作

安装的默认路径为`C:\Program Files\Jenkins`文件夹下，工作目录默认在此文件夹的子目录中。此路径可能存在文件夹权限相关问题，建议修改。

具体操作如下：

1. 在服务管理中停止Jenkins服务。
1. 使用管理员权限编辑Jenkins根目录中的*config.xml*文件，修改*  <workspaceDir>D:\JENKINS</workspaceDir>*项为指定路径。
1. 启动jenkins服务。

### Jenkins相关准备

* 在Jenkins配置页面选择`Configure Credentials`添加版本控制工具使用的验证信息。
* 在Jenkins配置页面选择`插件管理-可选插件`搜索`MSBuild Plugin`进行安装并重启。
* 【可选】`Blue Ocean Plugin`提供了一些比较美观的视图，根据需要选择安装。
* 配置MSBUILD：在Jenkins的配置页面，选择`Global Tool Configuration`。如下图所示配置MSBUILD工具

![MSBuild配置](jenkins-for-dotnet-framework/msbuild_config.png)
图中指定了`.NET 4.0`版本，可以根据实际需求修正路径至指定版本。也可点击`新增MSBuild`按钮同时添加多个版本备用。

## 

