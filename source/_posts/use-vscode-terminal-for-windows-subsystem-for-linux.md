---
title: 使用vscode terminal作为Windows下Linux子系统终端
date: 2017-07-18 17:13:48
tags: [WSL,VScode,terminal]
---
### 起因
 由于想使用commitizen对git message格式化，但在Windows下npm安装的全局包总是无法访问。因此决定将npm的第三方命令行工具转移至Linux子系统使用。目的是兼容npm因操作系统不同导致的兼容问题。
 此举有如下两个好处：
 - 为Windows平台部分提供Linux命令行。比如git，node.js的第三方工具。
 - 为Linux平台提供Windows GUI。

### 使用方法
 - 启用Windows Subsystem for Linux：百度就有，不赘述。
 - 更改VScode配置文件，使其直接调用子系统中的bash。
    使用VScode中集成的Terminal的原因是cmd及powershell的外观太丑了。
    作为替代的ConEmu在显示时存在换行bug。
    js实现的Hyper在Windows平台功能不完善：无法开启设置页面、存在中文输入bug。
    VScode可以通过配置文件对默认调用的终端类型进行配置。Menu -> File -> Preferences -> Setting：
``` json
{
    // 上略
    "terminal.integrated.shell.windows":
     "C:\\windows\\Sysnative\\bash.exe",
    "terminal.integrated.enableBold": false
}
```

