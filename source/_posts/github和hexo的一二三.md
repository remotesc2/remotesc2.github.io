---
title: github和hexo的一二三
date: 2016-06-22 23:07:55
tags:
- git
- github
- hexo
- next
categories: 
- 学习
- front-end
- git
---

github和hexo的一二三
===

盲点
---

### 关于git的一二三

之前只是知道 git 是个什么玩意，github 是个什么玩意，但对于怎么用却一概不知，现在傻了吧！书到用时方恨少，赶紧补起......  

git 的基本操作分为5种

+ git clone
+ git remote
+ git fetch
+ git pull
+ git push

一个个的来，这些只是都来自于阮大大的博客，记录一下，吸收吸收。

![git的简略图](./2016062201.jpg)

<!--more-->

#### git clone

克隆版本，相当于从服务器克隆一份代码到本机。先 `cd` 到用于存放代码的目录下，然后执行下方命令

```
git clone <远程版本库地址>
// 没用过这种
git clone <远程版本库地址> <本地目录名>
```

例如，我想 clone github 上的一个仓库

```  
git clone git@github.com:remotesc2/remotesc2.github.io.git
```

#### git remote

git 要求每个远程主机都必须指定一个主机名，默认名是 **origin**。`git remote` 命令就用于管理主机名。  

```
git remote
-->
origin
```

`git remote -v` 用于查看远程主机的网址  

```
git remote -v
-->
origin	https://github.com/remotesc2/remotesc2.github.io.git (fetch)
origin	https://github.com/remotesc2/remotesc2.github.io.git (push)
```

如果想用其他的主机名，需要用 `git clone` 的 `-o` 选项，例如

```
git clone -o newOrigin git@github.com:remotesc2/remotesc2.github.io.git

git remote
-->
newOrigin
```

`git remote show`，用于查看远程主机的详细信息  

```
git remote show <主机名>

git remote show newOrigin
```

`git remote add`，用于添加远程主机

```
git remote add <主机名> <网址>

...
```

`git remote rm` 用于删除远程主机  

```
git remote rm <主机名>

git remote rm newOrigin
```

`git remote rename`，用于为远程主机改名

```
git remote rename <原主机名> <新主机名>

git remote rename origin otherOrigin
```

#### fit fetch

当远程主机的代码库有了更新，git里面叫做 commit，当要把更新取回本地，这时候就要用到 `fit fetch` 命令。

```
git fetch <远程主机名>

git fetch origin
```

`git fetch` 通常用来查看其他人的进程，因为它取回的代码对你本地的开发代码没有影响，这点还没明白，[坑](www.baidu.com?wd=git fetch)。默认情况下，fetch 会把所有分支都进行更细，如果指向更新某一个分支  

```
git fetch <远程主机名> <分支名>

git fetch origin master
```

#### git pull

从远程主机取回某个分支，并与本机代码库进行合并

```
git pull <远程主机名> <远程分支名>:<本地分支名>
```

比如，要取回 **origin** 中 **hexo** 分支，与本地 **master** 进行合并  

```
git pull <远程主机名> <远程分支名>:<本地分支名>

git pull origin hexo:master
```

如果是与本地的 **hexo** 分支进行合并，那么就可省略后一部分属性

```
git pull origin hexo
```

#### git push

本地分支的更新，推送到远程主机

```
git push <远程主机名> <本地分支名>:<远程分支名>
```

#### github 流程

**create a new repository on the command line**

```
touch README.md
git init
git add README.md
git commit -m "first commit"
git remote add origin git@github.com:remotesc2/remotesc2.github.io.git
git push -u origin master
```

**push an existing repository from the command line**

```
git remote add origin git@github.com:remotesc2/remotesc2.github.io.git
git push -u origin master
```

### mac 上怎么创建ssh

使用 `git clone` 命令从 github 上克隆代码库时，如果使用SSH链接（例如：git@github.com:remotesc2/remotesc2.github.io.gitt），但你的 **SSH key** 并没有添加到 github 帐号设置中，会出现以下错误：  

```
Permission denied (publickey).
fatal: The remote end hung up unexpectedly
```

可先通过命令 `cd ~/.ssh` 来检查本机是否已经存在ssh。  

如果不存在，这时候就需要在本地创建 **SSH key**，命令如下：

```
ssh-keygen
```

然后系统提示输入文件保存位置等信息，一般连续敲三次回车即可，生成的 **SSH key** 文件保存在中 `~/.ssh/id_rsa.pub`  

如果存在，可以直接用文本编辑工具打开该目录下的文件，我用的是 atom，所以命令如下：

```
atom ~/.ssh/id_rsa.pub
```

接下来，拷贝 `~/.ssh/id_rsa.pub` 文件内的所有内容，打开 github 的添加 **SSH key** [界面](https://github.com/settings/keys)，填一个你自己喜欢的名称，并把拷贝的内容粘帖到 key 一栏，点“add key”按钮保存。

最后就是验证是否连接上 github，使用命令如下：

```
ssh -T remotesc2@github.com
```

如果出现下面提示，说明连接成功

```
Hi remotesc2! You've successfully authenticated, but GitHub does not provide shell access.
```

### git push 中碰到的问题

#### error: "git add README.md" returns "fatal: path spec 'README.md' did not match any files'

1. Open your text editor of choice.
2. Create a file, and make sure you name it 'README.md' (you can rename the file with the .md file extension after you save it).
3. Save to your desktop or wherever.
4. Drag this file to your git repository.
5. In Terminal (for Mac users) type 'git add README.md'
6. Type 'ls' to confirm the file has been committed (it should just show up as 'README.md' on the next line in terminal).

#### error: src refspec master does not match any

引起该错误的原因是，目录中没有文件，空目录是不能提交的，可以新建一个文件......

```
touch README
git add README 
git commit -m 'first commit'
git push origin master
```
提交代码的大致流程如下：

```
1. 在github上创建项目

2. 使用git clone https://github.com/xxxxxxx/xxxxx.git克隆到本地

3. 编辑项目

4. git add . （将改动添加到暂存区）

5. git commit -m "提交说明"

6. git push origin master 将本地更改推送到远程master分支。
```

但是如果远程主机已经有文件了，就会出现错误。这时候应该先 pull 一次，即

```
git pull origin master
```

注：pull = fetch + merge  
然后再 push，即

```
git push origin master
```

### 今天的主旨

之所以会引出这么多的**盲点**，就是因为这么一个需求——*在 GitHub Pages 的仓库中，创建两个分支，一个用来存放 Hexo 网站文件，一个用来发布网站*  
流程基本如下：
git@github.com:remotesc2/remotesc2.github.io.git

1. 创建仓库，remotesc2.github.io.git
2. 选择一个文件夹，使用 GitHub Desktop 将仓库 clone 到此文件夹
3. 将 Hexo 网站文件放到此文件夹中
4. 提交一个新分支 hexo
5. 将 hexo 设置为默认分支（默认情况下， master是默认分支）
6. 修改 _config.yml 中的 deploy 参数，分支应为 master
7. 这时就可通过 GitHub Desktop 将 Hexo 网站文件 push 到 hexo 分支；通过 `hexo d`将发布网站 push 到 master 分支

参考
---
1. [http://www.ruanyifeng.com/blog/2014/06/git_remote.html](http://www.ruanyifeng.com/blog/2014/06/git_remote.html)
2. [http://www.cnblogs.com/heyonggang/p/3462191.html](http://www.cnblogs.com/heyonggang/p/3462191.html)
3. [http://justcoding.iteye.com/blog/1829693](http://justcoding.iteye.com/blog/1829693)
4. [http://www.jianshu.com/p/8d26730386f3](http://www.jianshu.com/p/8d26730386f3)
5. [http://crazymilk.github.io/2015/12/28/GitHub-Pages-Hexo搭建博客/#more](http://crazymilk.github.io/2015/12/28/GitHub-Pages-Hexo搭建博客/#more)
