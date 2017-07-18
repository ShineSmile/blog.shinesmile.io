---
title: 格式化git commit message
date: 2017-07-18 18:46:11
tags:
---

``` bash
> npm install -g commitizen
> npm install -g cz-conventional-changelog
> echo '{ "path": "cz-conventional-changelog" }' > ~/.czrc

```
安装成功后将`git commit <option>` 换为 `git cz <option>`。

![git cz -a](format-git-commit-message/git_cz.png)

Windows用户若在使用npm过程中出现全局包无法访问的问题可以直接[在Windows下使用Linux bash并且使用vscode作为默认终端WSL](../use-vscode-terminal-for-windows-subsystem-for-linux)。
