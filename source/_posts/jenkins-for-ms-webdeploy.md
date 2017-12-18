---
title: 使用Jenkins通过Web Deploy进行持续集成
date: 2017-12-14 17:29:29
tags: [jenkins,web deploy]
---
提到ASP.NET的项目就不得不提Web Deploy，此工具被Visual Studio集成（需要安装Web开发相关功能）。若想脱离Visual Studio使用相关功能可以在这里[下载](https://www.iis.net/downloads/microsoft/web-deploy)。当然也可通过微软的Web平台安装程序进行安装。
![通过Web平台安装程序进行安装](jenkins-for-ms-webdeploy/web_deploy_install.png)

## IIS管理服务的安装
若要使用Web Deploy相关功能。需要安装IIS的管理服务：
![IIS管理服务的安装](jenkins-for-ms-webdeploy/iis_management_service_install.png)
安装完成后需要配置相关权限，建议使用独立的IIS管理账户进行配置，本文档将直接使用Administrator账户进行演示。

## Web Deploy权限设置
![配置权限](jenkins-for-ms-webdeploy/web_deploy_authorization.png)
双击权限管理添加相应用户后即可对此Web服务进行发布。
![添加用户](jenkins-for-ms-webdeploy/authorization_account.png)
* 注意右上角**只接受具有Windows凭据的账户**的提示。

安装完毕后需要通过服务管理确定**Web Management Service**已正常启动。
![服务管理](jenkins-for-ms-webdeploy/web_management_service.png)

## 工程相关配置

可以通过右键工程-发布根据发布向导完成相关的配置：
![发布设置](jenkins-for-ms-webdeploy/project_configuration.png)
相关信息添加好后，点击Validate Connection会对链接进行测试。为防止中间人攻击，需要人工对证书进行校验。
* 如在*Server*处添加*localhost*地址，Visual Studio会要求使用管理员账户启动且无需添加用户名等信息，否则无法发布。本人对此策略感到疑惑：同样是通过服务发布，远程发布只要拥有IIS服务管理权限即可，而向本机发布却需要完整的管理员权限。向本机发布的严苛程度甚至高过远程发布。莫名其妙，望高手拍砖。

![发布设置](jenkins-for-ms-webdeploy/publish_configuration.png)
Setting页面会对编译选项、是否移除目标路径现有的文件、预编译、是否排除App_Data文件夹中的内容以及数据库链接进行配置。
设置好后可以进行保存，并通过发布按钮发布进行测试。

## Jenkins集成
若要通过命令行调用Web Deploy相关功能，只需在MSBuild工具附加下述参数：*p:DeployOnBuild=True;PublishProfile=WebDeploy-Demo;Password=Dawn233+;AllowUntrustedCertificate=true*

|键|值|说明|
|---|---|---|---|
|DeployOnBuild|*True* or *False*|编译后是否发布|
|PublishProfile|*ProfileName*|使用的发布文件。**注意：不需要添加发布文件的扩展名。**|
|Password|*发布用户的密码*|**此方式会在脚本中暴露对应用户的密码，建议使用证书进行用户认证。**|
|AllowUntrustedCertificate|*True* or *False*|**具有中间人攻击的安全风险，建议使用证书进行用户认证。**|
在编译成功后，MSBuild会直接调用Web Deploy相关的功能进行发布。
JenkinsFile脚本示例如下：
``` groovy
node('windows'){
    stage ('Deploy AsGet API'){
        timeout (3){
            def IsDeploy = 
            input(
                id: 'IsDeploy',
                message: "Check service to deploy:", 
                parameters: 
                    [
                        booleanParam(defaultValue: false, name: 'ProjectName')
                    ]
            )
            if(IsDeploy == "true"  || IsDeploy){
                bat 'msbuild Project.To.Build.csproj /m /t:Rebuild /p:Configuration=Release;DeployOnBuild=True;PublishProfile=WebDeploy-Demo;Password=Dawn233+;AllowUntrustedCertificate=true'
            }
            else{
                echo 'Input Time out(3min).'
            }
        }
    }
}
```