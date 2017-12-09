---
title: 添加Jenkins slave节点
date: 2017-12-08 14:07:13
tags: [jenkins]
---
jenkins slave节点可以用来扩展jenkins的性能，提高工程编译的并发性。更重要的一点，可以对平台进行扩展，完成master所在节点无法完成的编译工作。
**注意：添加slave节点无需在目标服务器安装完整的jenkins服务，连接到slave节点后，master会将slave.jar发送至目标服务器。**
## 添加新的Linux slave节点：
添加linux节点的方式非常简单，只要能通过ssh的方式登录到目标服务器，像下图一样进行简单的配置即可进行连接：
![添加linux slave节点](jenkins-master-slave/linux_slave_node_config.png)
* 图中模糊的地方分别是指定该节点的标签（Lables）以及使用强度（Usage：尽可能多的使用，只执行标签匹配的任务）.
* 图中Host Key Verification Strategy有四个选项，分别是已知服务器、使用ssh key pair、使用ssh用户名密码、无需认证。连接时需要注意防止中间人攻击。
* 图中的Lables就是节点的标签，若在jenkinsfile中指定了节点的名称，jenkins会在指定的节点执行节点中的任务。
## 添加Windows slave节点：
这里有一篇使用jnlp文件的方式添加windows slave节点的官方文档。 在master节点配置好后下载一个slave-agent.jnpl的文件在指定的windows机器上执行即可，非常简单。
**注意：windows slave节点添加好后，在尝试添加新服务时出现了问题，因此此节点暂时只能通过手动的方式启动。(Jave ver.9.01)**