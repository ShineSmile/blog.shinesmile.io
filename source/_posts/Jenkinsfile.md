---
title: Jenkinsfile基础
date: 2017-12-08 14:06:55
tags: [jenkins, jenkinsfile]
---

除了使用Jenkins自由风格构建方式通过Web GUI进行配置外，还可以使用Jenkinsfile将构建过程校本化。
JenkinsFile通常保存在工程的根目录并将其添加至版本控制系统，还可以通过建立新的仓库对Jenkinsfile统一管理。

## 简介Jenkinsfile的语法
```jenkinsfile
node ("Windows"){
    stage ("Checkout source code from SVN"){
        checkout(...) 
    }
    
    stage ("Restore Nuget Package"){
        bat 'cmd /c "C:\\Program Files (x86)\\Microsoft Visual Studio\\2017\\Enterprise\\MSBuild\\15.0\\Bin\\nuget.exe" restore SolutionToBuild.sln'
    }
    
    stage ("Build From Source"){
        bat 'cmd /C "C:\\Program Files (x86)\\Microsoft Visual Studio\\2017\\Enterprise\\MSBuild\\15.0\\Bin\\msbuild.exe" /m /t:rebuild /p:Configuration=Release SolutionToBuild.sln'
    }

    try{
        stage ("Run Unit Test"){
            bat 'cmd /c "C:\\Program Files (x86)\\Microsoft Visual Studio\\2017\\Enterprise\\Common7\\IDE\\MSTest" /testcontainer:Path\\To\\UnitTest1.dll'
            bat 'cmd /c "C:\\Program Files (x86)\\Microsoft Visual Studio\\2017\\Enterprise\\Common7\\IDE\\MSTest" /testcontainer:Path\\To\\UnitTest2.dll'
        }
    }
    catch(error){
        throw error
    }
    finally{
        stage ("Collect Test Result Files"){
            step([$class: 'MSTestPublisher', testResultsFile:"**/*.trx", failOnError: true, keepLongStdio: true])
        }
        stage ("Archives the Binaries"){
            archiveArtifacts 'Path\To\Build\Result1\*'
            archiveArtifacts 'Path\To\Build\Result2\*'
        }
    }
}
```
Tips:
* 可以通过node("<节点Lable>")来制定用来构建的节点
* checkout步骤的代码可以通过Jenkins提供的GUI工具配置生成
* 执行批处理的模板为`bat 'cmd /c command param file'`，注意，单引号中的内容若存在`\`则需要转义。此过程代码同样可以通过模板生成。
* `try{ }catch{ }finally{ }`：其逻辑与C#中的代码类似，不论`try`块是否成功，`finnally`块都会执行。

