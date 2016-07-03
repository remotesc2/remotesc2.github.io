---
title: SVN 不懂
date: 2016-06-28 22:39:23
tags:
- SVN
categories: 
- 学习
- front-end
- SVN
---

对于 SVN 的巩固
===

盲点—— SVN 的种种
---

SVN 平常只是简单的使用过它的 `Checkout`，`Commit`，`Update`......然后就啥也不知道了。上周末刚刚把 git 基础啃了遍，想来也要重新梳理下 SVN 的基础点吧！

SVN 安装
---

很不幸，SVN 的世界中最好用的也就是：服务端——VisualSVN server、客户端——TortoiseSVN，在 mac 尝试了若干款 SVN 客户端，终究还是回归到虚拟机+小乌龟。

## VisualSVN server 基础操作

安装过程中，会有一步——**Standard Edition**，在随后的窗口包含如下内容：Location 是指VisualSVN Server 的安装目录，Repositorys 是指定你的版本库目录，Server Port 指定一个端口，Use secure connection 表示是否使用安全连接。

安装完成以后，便可启动 VisualSVN Server Manager。在左边菜单里可以看到三个节点：仓库集合——Repositories、用户——Users、组——Groups。

### 一、创建仓库

Repositories 右键 --> 新建 --> Repository... --> 新建仓库对话框  
假设，创建一个新仓库——newRepository

### 二、新建用户

Users 右键 --> User... --> 新增用户

### 三、新建组

Groups 右键 --> Group... --> 新增组

### 四、给仓库加用户权限

newRepository 右键 --> Properties... --> 在弹出的对话框，选择 Security 选项卡


## TortoiseSVN 基础操作

### 一、克隆代码（Checkout）

在一个空文件夹——test 中右键，`SVN Checkout`

### 二、提交新增文件（Add）

在 test 文件夹中新增一个文件 b.js，提交 b.js 的方法：  

1. 先提到变更列表中，再 commit 到配置库中，选择新增文件，右键 SVN 菜单执行“Add“操作提交到”变更列表中”，然后右键 SVN 菜单执行”SVN Commit”提交到版本库中。
2. 不提交到变更列表中，而是直接 commit 配置库中，选择该文件，右键 svn 菜单执行”SVN Commit”操作。

### 三、删除文件（Delete）

假设要删除 c.js 文件：  

1. 右键 c.js ，选择 SVN 菜单执行“delete”操作，然后选择被删除文件的父目录，右键 SVN 菜单执行“SVN Commit”.
2. 使用操作系统的删除操作删除该文件，然后选择被删除文件的父目录，右键 SVN 菜单执行”SVN Commit”，在变更列表中选择被删除的文件

### 四、重命名（Rename）

选中需要重命名的文件或文件夹，然后右键”TortoiseSVNàRename“，在弹出的对话框中输入新名称，点击”ok”按钮，并将修改文件名后的文件或文件夹通过 “SVN Commit”提交到 SVN 服务器上。

### 五、还原（Revert）

右击想要回退的文件或者文件夹，在TortoiseSVN弹出菜单中选择“Update to reversion…”

### 六、检查更新(Check for modifications)

明天对下这个功能，感觉从来没有见过......

### 七、更新代码（update）

### 八、代码提交（Commit）

分支与合并
---

假如我本地新建一个文件夹 test 下有 2 个文件夹 trunk（存放主干上的代码）和 branch（存放分支上的代码）

### 一、提取主干（trunk）上的代码

点击trunk（文件夹） --> 鼠标右键 --> 点击SVN Checkout

### 二：新建分支

右键 trunk（文件夹），选择 TortoiseSVN --> branch/Tag。弹出框内，在 **To path** 栏填写新分支的路径`“/newBranch”`（服务端），这时候在服务端仓库下就会产生一个分支——newBranch。

然后进入 branch 文件夹，右键 **SVN Checkout**，后头的操作就和一般 Checkout 一样。

### 三、合并（分支融入主线）

所谓有分就有合，分支只是为了某个 feature 而生......终究要进行合并、删除，这是它无法逃避的命运！

右键 trunk（文件夹），选择 TortoiseSVN --> Merge，弹框选择第二个**“Merge two different trees”**；下一步，需要填写主线、分支的 url，第一个框是主线 url，第二个为分支 url；下一步，点击 **Merge** 即可，这时候在 trunk（文件夹）下就可以看到 branch（文件夹）中提交过的内容

### 四、合并（主线融入分支）

有时也会遇到这种情况，分支创建后，主线进行了基础更新，分支也需要用到这些更新。其实整个过程类似于*“三、合并（分支融入主线）”*，只不过将顺序反过来......

右键 branch（文件夹），选择 TortoiseSVN --> Merge，弹框选择第二个**“Merge two different trees”**；下一步，需要填写分支、主线的 url，第一个框是分支 url，第二个为主线 url；下一步，点击 **Merge** 即可，这时候在 branch（文件夹）下就可以看到 trunk（文件夹）中提交过的内容

### tag & branch

实现上，branch 和 tag，对于 SVN 都是使用 copy 实现的，所以他们在默认的权限上和一般的目录没有区别。至于何时用 tag，何时用 branch，完全由人主观的根据规范和需要来选择。

一般情况下，**tag** 是用来做一个 milestone，不管是不是 release，都是一个可用的版本。主要目的是提供一个可读（readable）的标记。**branch** 是用来做并行开发的，这里的并行是指和 trunk 进行比较。

### 实际生产环境

比如，1.0 开发完成，这个时候要做一个 tag，tag\_release\_1\_0，然后基于这个 tag 做release，比如安装程序等。trunk 进入 1.1 的开发，但是 1.0 发现一个 bug，那么就需要基于tag\_release\_1\_0 做一个 branch，branch\_bugfix\_1\_0，基于这个 branch 进行 bug 修复，等到 bug 解决，做一个tag，tag\_release\_1\_0\_1，然后，根据需要决定 branch\_bugfix\_1\_0 是否并入trunk。

想想
---

其实 SVN 的和 Git 的在日常使用中的思路一致，无外乎他俩的实现思路完全不同，对于 SVN 每个分支都是通过 copy 来实现这一点来说，Git 的思想确实领先于 SVN

参考
---

1. [http://www.cnblogs.com/armyfai/p/3985660.html](http://www.cnblogs.com/armyfai/p/3985660.html)
2. [http://blog.csdn.net/zoudaokou2006/article/details/6415452](http://blog.csdn.net/zoudaokou2006/article/details/6415452)
