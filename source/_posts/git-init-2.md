---
title: Git init 2：Git命令
date: 2017-02-20 19:39:00
tags:
---

## Git仓库的初始化：git init

### 本地仓库

``` bash
git init
```

此命令将所在文件夹初始化为git本地仓库。初始化成功的标志为，文件夹内创建了一个名为`.git`的隐藏子文件夹。`git`子文件夹为**本地仓库**，`.git`所在文件夹是版本控制工作区。

本地仓库的初始化：
![本地仓库](git-init-2/workfolder.png)

本地仓库文件结构：
![本地仓库的文件结构](git-init-2/local_repo.png)

本系列文章的标题即取自`git init`命令。

### 远端仓库

``` bash
git init --bare
```

`--bare`参数表示将当前文件夹初始化为不包括工作区的裸库。其文件结构与本地仓库相似，但某些参数不同。远端仓库主要用于本地仓库之间的同步。远端仓库不能提交代码。只能又本地仓库推送或获取。

![远端仓库](git-init-2/remote_repo.png)

## 查看变更的文件：git status

``` bash
git status
```

`git status`命令会列出当前所在的分支，发生变更的文件，以及相关提示。
![查看变更](git-init-2/status.png)

## 添加文件至缓存区：git add

### 添加一个文件

``` bash
git add <filename>
```

`git add`命令会将指定的文件的**当前状态**添加到**暂存区**中。注意这里的**暂存区**与**本地仓库**不是同一个位置。如果与SVN类比，相当于提交过程中对文件的勾选。**只有暂存的文件会在后续的提交中被提交。**

![添加文件至缓存区](git-init-2/modify_staged_file.png)

1. 上述过程创建了内容为*git add test*的txt文本文档。

2. 使用`git status`表示其为`untracked`，即未被版本控制追踪。

3. 使用`git add`将其添加至缓存区后可以在`git status`中观察到其状态为`Changes to be commit`，即将被提交的变更。

4. 再次修改这个文件，可以看到文件即被缓存，也又修改。首先暂存区只会暂存执行`git add`命令时的文件状态，**后续的修改并不会被自动暂存，也不会被后续的提交操作提交。**

5. 若要将新的变更添加到缓存区，只需再次运行`git add`命令即可。

### 添加所有未被追踪的文件

``` bash
git add .
```

![添加所有未被追踪的文件](git-init-2/git_add_all.png)

## 将文件移出暂存区：git rm --cached

``` bash
git rm --cached <filename>
```

`git rm`命令会移除**暂存区**中指定的文件。不会对文件进行修改。

如果文件在暂存后再次被编辑，需要添加`-f`强制执行参数。`git rm -f --cached`。同样，命令仅仅会将文件从暂存区移除，文件的编辑结果不会丢失。

## 提交 git commit

``` bash
git commit -m "提交信息"
```

`git commit`会将暂存区里的数据提交到**本地仓库**。省略`-m`参数会调用Vim工具编辑提交信息。

## 远端管理：git origin

``` bash
git remote add <remote_name> <remote_url>
git remote remove <remote_name>
```

## 推送至远端：git push 

``` bash
git push <remote_name>
```

## 获取远端仓库：git fetch

``` bash
git fetch <remote_name>
```

## 克隆仓库：git clone

``` bash
git clone <remote_url>
```

## 分支管理：git branch

## 拉取某个分支：git pull


``` bash
```

## 合并分支：git merge

``` bash
```

