---
title: github和hexo的一二三（续2）
date: 2016-06-24 21:07:55
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

github和hexo的一二三（续2）
===

盲点—— git 的几个基本概念
---

### 如何创建本地版本库（又名仓库，repository）

版本库一句话说来就是一个文件夹，这个文件夹里的所有文件都可以被 git 管理起来，每个文件的增、删、改、查，git 都能进行追踪，打开 terminal

``` 
// 创建一个空的文件夹，作为版本库
$ mkdir learngit

// 进入 learngit
$ cd learngit

// 进行 git 初始化
$ git init
Initialized empty Git repository in /Users/remote/front-end/learngit/.git/
```

> `ls -ah` 可以查看到“.”开头文件  
> `pwd` 可以查看当前路径

### 创建完成后，如何添加文件

一、编写 learngit 下的第一个文件 readme.txt，

二、用命令 `git add` 告诉Git，将该文件添加进 git 仓库

```
$ git add readme.txt
```

这里没有任何提示信息，据说这个所谓 Unit 设计哲学“没有消息就是好消息”，鬼知道，反正我不习惯。

三、用命令 `git commit` 告诉 git，把文件提交到仓库

```
$ git commit -m "wrote a readme file"
[master (root-commit) cb926e7] wrote a readme file
 1 file changed, 2 insertions(+)
 create mode 100644 readme.txt
```

问题来了，为啥非要分为 `add` 和 `commit` 两步？
 
> git 系统分三个区：本地项目，缓存区，远程仓库。远程仓库只认从缓存区过来的代码，add 的作用就是选择本地项目里哪些文件将被添加到缓存区。对于这些概念，将在**《工作区 & 缓存区》**章节得到解释

同时，`commit` 一次可以提交多个文件，即

```
$ git add file1.txt
$ git add file2.txt file3.txt
$ git commit -m "add 3 files."
```

<!--more-->

### 仓库状态查看

一、对之前创建的 read.txt 进行修改，运行 `git status` 来查看当前仓库状态

```
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	modified:   readme.txt

no changes added to commit (use "git add" and/or "git commit -a")
```

> 上面的信息是说“readme.txt被修改过了，但还没有准备提交的修改”

二、现在知道了 readme.txt 这个文件被修改了，但是具体修改了什么内容，就要使用 `git diff` 命令

```
$ git diff
diff --git a/readme.txt b/readme.txt
index 46d49bf..9247db6 100644
--- a/readme.txt
+++ b/readme.txt
@@ -1,2 +1,2 @@
-Git is a version control system.
+Git is a distributed version control system.
 Git is free software.
remote-mba:learngit remote$ 
```

> 通过对比，发现增加了一个 **distributed** 单词

三、在知道具体改变了什么之后，就可以执行 `git add`，还是没有任何提示

```
$ git add readme.txt
```

四、再次运行 `git status`

```
$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

	modified:   readme.txt
```

> 将被提交的是修改后的 readme.txt

四、执行 `git commit`

```
$ git commit -m "add distributed"
[master bdf9991] add distributed
 1 file changed, 1 insertion(+), 1 deletion(-)
```

五、在执行 `git status`

```
$ git status
On branch master
nothing to commit, working directory clean
```

> 没有需要提交的修改，工作目录是干净的......

### 版本回退

一、再次修改 readme.txt ，然后进行提交

```
$ git add readme.txt
$ git commit -m "append GPL"
[master 846a000] append GPL
 1 file changed, 1 insertion(+), 1 deletion(-)
```

现在 readme.txt 已经经历了三次修改，也就是三个版本。可以使用 `git log` 进行查看

```
$ git log
commit 846a000b757ac5ff13103419de2ca82dbc8cf8aa
Author: remotesc2
Date:   Fri Jun 24 22:33:03 2016 +0800

    append GPL

commit bdf9991b45791b11b3269d02b6ea0e953e86abc9
Author: remotesc2
Date:   Fri Jun 24 22:28:27 2016 +0800

    add distributed

commit 3afae671cc9465686bb8a102debdb84be727649f
Author: remotesc2
Date:   Fri Jun 24 21:56:17 2016 +0800

    wrote a readme file
```

这里有个精简参数 `--pretty=oneline`

```
$ git log --pretty=oneline
846a000b757ac5ff13103419de2ca82dbc8cf8aa append GPL
bdf9991b45791b11b3269d02b6ea0e953e86abc9 add distributed
3afae671cc9465686bb8a102debdb84be727649f wrote a readme file
```

二、如果想退回上个版本，可以使用 `git reset` 命令  
在 git 中，`HEAD` 代表上个版本，也就是版本库里最新的一个版本，`HEAD^`代表上上个版本。上上上...50个上版本该如何呢？还靠**“^”**肯定很傻，可以写成 `HEAD~50`

```
$ git reset --hard HEAD^
HEAD is now at bdf9991 add distributed
```

> --hard 意义不明

三、如果想再回到最新的版本该怎么办？  

如果你还记得最新版本的 **commit id**

```
$ git reset --hard 846a000b757ac5ff13103419de2ca82dbc8cf8aa
HEAD is now at 846a000 append GPL
```

如果你实在不记得了，那就需要使用命令 `git reflog`

```
$ git reflog
846a000 HEAD@{0}: reset: moving to 846a000b757ac5ff13103419de2ca82dbc8cf8aa
bdf9991 HEAD@{1}: reset: moving to HEAD^
846a000 HEAD@{2}: commit: append GPL
bdf9991 HEAD@{3}: commit: add distributed
3afae67 HEAD@{4}: commit (initial): wrote a readme file
```

> 第一条是因为我上面已经回到了最新版，第三条就是之前在做回滚操作时候的 `commit id` ——**846a000**，这样就又可以回归想要版本

四、`git reset` 不仅可以回到历史版本，同时还可以把缓存区里的修改撤销掉（unstage），重新放回工作区

当进行了错误添加（`git add`）时，可以使用此方法将错误内容从缓存区清除，避免进行提交（`git commit`）

```
$ git reset HEAD readme.txt
```

> HEAD 表示最新版本

### 工作区 & 缓存区

+ 工作区——learngit 这个文件夹
+ 版本库（Repository）——工作区有一个隐藏目录 **.git**，即 git 的版本库。里面存放了很多内容，其中最重要的就是被称作 stage（有的地方也叫作 index）的缓存区。除此之外，还有 git 默认创建的 **master** 分支和指向该分支的 **HEAD**

现在就可以说明一下为什么会有 `git add` 和 `git commit` 两个命令。  
`git add`：将文件添加进 stage（缓存区）  
`git commit`：只会将缓存区内的内容提交到当前分支，同时清空缓存区。如果没有 `git add` 的文件是不会被提交的

### 撤销修改

`git checkout`，丢弃工作区内的内容，恢复到之前的版本。这里的“之前的版本”有两种情况：  

1. readme.txt 自修改后，还没有进行 `git add`，那么撤销恢复到和版本库里的状态  
2. readme.txt 进行了 `git add` 后又进行了改动，那么撤销恢复到的缓存区里的状态

```
$ git checkout -- readme.txt
```

### 删除文件

一、先新增一个文件 test.txt，然后  

```
$ git add test.txt
$ git commit -m "add test.text"
[master c035f16] add test.text
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 test.txt
```

二、删掉这个文件，可以使用 `rm test.txt`，使用 `git status` 查看仓库

```
$ git status
On branch master
Changes not staged for commit:
  (use "git add/rm <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	modified:   readme.txt
	deleted:    test.txt

Untracked files:
  (use "git add <file>..." to include in what will be committed)

	LICENSE

no changes added to commit (use "git add" and/or "git commit -a")
```

> git 会提示 test.txt已经被删除

这时，一般有两种可能：一种就是从版本库中把这个文件删除 `git rm`，然后 `git commit`

```
$ git rm test.txt
rm 'test.txt'

$ git commit -m "remove test.txt"
[master 24b0dfb] remove test.txt
 1 file changed, 0 insertions(+), 0 deletions(-)
 delete mode 100644 test.txt
```

另外一种就是这个文件属于误删，需要找回来。要用到 `git checkout` 将误删的文件恢复到最新的版本

```
$ git checkout -- test.txt
```

> `git checkout` 其实是用版本库里的版本替换工作区的版本

git 的远程库
---

### 添加远程库

一、申请 github，创建一个新的 reposition  
二、本地版本库与远程库进行关联，在本地仓库文件夹下，执行 `git remote add`

```
$ git remote add origin git@github.com:remotesc2/learngit.git
```

> 远程库的名字就是 origin，这是 git 默认的叫法。虽然可以修改，但是这个看起来一目了然。

三、把本地库的所有内容推送到远程库上，使用 `$ git push <远程主机名> <本地分支名>:<远程分支名>`，如果省略远程分支名，则表示将本地分支推送与之存在”追踪关系”的远程分支（通常两者同名），如果该远程分支不存在，则会被新建

```
$ git push -u origin master
Counting objects: 13, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (9/9), done.
Writing objects: 100% (13/13), 1.07 KiB | 0 bytes/s, done.
Total 13 (delta 2), reused 0 (delta 0)
To git@github.com:remotesc2/learngit.git
 * [new branch]      master -> master
Branch master set up to track remote branch master from origin.
```

> 把当前分支master推送到远程库，由于远程库是空的，我们第一次推送 master 分支时，加上了 **-u** 参数，**-u** 指定 origin 为默认主机，这样后面就可以不加任何参数使用git push。

从此，只要本地库做了修改，需要提交，就可以通过如下命令

```
$ git push origin master
```

> 把本地 **master** 分支的最新修改推送至 GitHub

如果尝试上面提到的不加参数的 `git push` 命令

```
$ git push

warning: push.default is unset; its implicit value has changed in
Git 2.0 from 'matching' to 'simple'. To squelch this message
and maintain the traditional behavior, use:

  git config --global push.default matching

To squelch this message and adopt the new behavior now, use:

  git config --global push.default simple

When push.default is set to 'matching', git will push local branches
to the remote branches that already exist with the same name.

Since Git 2.0, Git defaults to the more conservative 'simple'
behavior, which only pushes the current branch to the corresponding
remote branch that 'git pull' uses to update the current branch.

See 'git help config' and search for 'push.default' for further information.
(the 'simple' mode was introduced in Git 1.7.11. Use the similar mode
'current' instead of 'simple' if you sometimes use older versions of Git)
```

> git 要我们确定使用哪种方式来处理不带参数的 `git push` 命令

这里我可以先使用 **simple** 模式

```
$ git config --global push.default simple
```

只要之前有使用 `git git push -u origin master` 进行默认关联的，以后推送都可以使用不带参数的 `git push` 来进行

### 克隆远程库

`git clone`，从远程库克隆一份代码到本地，会在当前目录创建一个新的文件夹用于存放远程库的克隆下来的代码

```
$ git clone git@github.com:remotesc2/gitskills.git
Cloning into 'gitskills'...
remote: Counting objects: 3, done.
remote: Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
Receiving objects: 100% (3/3), done.
Checking connectivity... done.

$ cd gitskills
$ ls
```

克隆完成以后，别忘了设置自己的名字以及邮件

```
$ git config user.name "re"
$ git config user.email "re@example.com"
```

`git config` 命令有个 **--global** 参数，使用了这个参数表示这台机子上所有的 git 都是用这个配置

```
$ git config --global user.name "re"
$ git config --global user.email "re@example.com"
```

分支
---

*重点来了！！！*  
分支有啥用？你在修改一个功能，当这个功能还未开发完时，你并不能直接将未完成代码进行上传，因为这样很可能导致项目编译失败。同时你又担心在开发过程中，如果不进行代码上传，玩意丢失怎么办。这个时候，分支的作用就来了。你可以自己创建一个项目分支，进行你日常开发功能上传，当整个功能开发结束后，再将自己的合并到原来的分支里。  

### 创建分支

git 会把每次提交串成一条时间线。从开篇到现在，只用到了 git 默认提供的一个分支——master。

> 应该可以这样理解吧，所谓的 master 就是一个指针，这个指针在时间线上走过的部分，就是 master 分支

在**《版本回退》**里提到的 HEAD 和分支的关系应该是这样：HEAD 指向 master，master 指向提交。每次提交，master 分支就会向前增长一步。

当创建一个分支 dev 时，dev 指向 master 相同的一次提交。随后整个关系变成：HEAD 指向 dev，dev 指向提交。创建分支以后，工作区的修改和提交都将针对 dev 这个分支。每次提交，dev 指针向前走一步，而 master 指针原地不动。

至于分支合并，最简单的场景就是将 master 指针指向 dev 当前的提交，合并完成后，甚至可以把 dev 这个分支删除

使用 `git checkout` 命令创建分支

```
$ git checkout -b dev
M	readme.txt
Switched to a new branch 'dev'
```

> `git checkout` 命令加上 **-b** 参数表示创建并切换，相当于以下两条命令 `git branch dev` 和 `git checkout dev`

### 查看分支

用 `git branch` 命令查看当前仓库分支

```
$ git branch
* dev
  master
```

> 当前分支前面会标一个*号

### 对分支进行提交

```
$ git add readme.txt 
$ git commit -m "branch test"
[dev 8120b4d] branch test
 1 file changed, 2 insertions(+)
remote-mba:learngit remote$ 
```

### 合并分支

先切换回 master 分支

```
$ git checkout master
Switched to branch 'master'
Your branch is up-to-date with 'origin/master'.
```

使用 `git merge` 命令进行分之合并

```
$ git merge dev
Updating 24b0dfb..8120b4d
Fast-forward
 readme.txt | 2 ++
 1 file changed, 2 insertions(+)
```
合并成功以后，就可以把 dev 分支删除掉了

```
$ git branch -d dev
Deleted branch dev (was 8120b4d).
```

再用 `git branch` 进行仓库分支查看

```
$ git branch
* master
```

就只剩下 master 这一个分支

### 分支冲突

*又是一个重点，之前在使用 SVN 就经常碰到这个头疼问题*  

----

2016-06-25 00:29，学海无涯，该就睡就。明天继续......

----

书接上文

首先，人为的创造一次冲突。

一、创建一个分支

```
$ git checkout -b feature1
Switched to a new branch 'feature1'
```

二、修改 **readme.txt** 最后一行内容。

修改前：

```
Creating a new branch is quick.
```

修改后：

```
Creating a new branch is quick AND simple.
```

三、提交 feature1 分支

```
$ git add readme.txt
$ git commit -m "AND Simple>"
[feature1 1ce6a6a] AND Simple
 1 file changed, 1 insertion(+), 1 deletion(-)
```

四、切换回 master 分支

```
$ git checkout master
Switched to branch 'master'
Your branch is ahead of 'origin/master' by 1 commit.
  (use "git push" to publish your local commits)
```

> git 会自动提示我们本地 master 分支比远程 master 分支多了 1 次提交。

五、让我继续制造冲突，在 master 分支下，继续修改 **readme.txt** 的最后一行

```
Creating a new branch is quick & simple.
```

六、提交

```
$ git add readme.txt
$ git commit -m "& simple"
[master 6f866ce] & simple
 1 file changed, 1 insertion(+), 1 deletion(-)
```

现在分支 master 和 feature1 都有了新的提交，时间线上出现了两条。如果这是时候使用《分支合并》中的 `git merge` 会如何？

七、合并分支

```
$ git merge feature1 
Auto-merging readme.txt
CONFLICT (content): Merge conflict in readme.txt
Automatic merge failed; fix conflicts and then commit the result.
```

> 在合并 readme.txt 文件时候发生冲突，自动合并失败......

使用 `git status` 查看会如何？

```
$ git status
On branch master
Your branch is ahead of 'origin/master' by 2 commits.
  (use "git push" to publish your local commits)
You have unmerged paths.
  (fix conflicts and run "git commit")

Unmerged paths:
  (use "git add <file>..." to mark resolution)

	both modified:   readme.txt

no changes added to commit (use "git add" and/or "git commit -a")
```

八、打开 readme.txt 进行查看

``` txt
...
Git has a mutable index called stage.
<<<<<<< HEAD
Creating a new branch is quick & simple.
=======
Creating a new branch is quick AND simple.
>>>>>>> feature1
```

> git用 <<<<<<<，=======，>>>>>>> 标记出不同分支的内容，HEAD 代表当前分支

我们去除 feature1 分支的修改

```
...
Git has a mutable index called stage.
Creating a new branch is quick & simple.
```

再次提交

``` 
$ git add readme.txt
$ git commit -m "conflict fixed"
[master d13db4c] conflict fixed
```

这时候提示提交成功，此时 master 分支提交数又增加了 1 次。使用带参数的 `git log` 命令可以查看分支合并情况

```
$ git log --graph --pretty=oneline --abbrev-commit
*   d13db4c conflict fixed
|\  
| * 1ce6a6a AND Simple
* | 6f866ce & simple
|/  
* 8120b4d branch test
* 24b0dfb remove test.txt
* c035f16 add test.text
* 846a000 append GPL
* bdf9991 add distributed
* 3afae67 wrote a readme file
```

> `git log` 后头跟着的这一堆参数都是啥意思啊？  
> `--graph` ——查看分支合并  
> `--pretty=oneline` ——缩略显示，忽略 **Author** 和 **Date**  
> `--abbrev-commit`——缩减 **commit id**，只显示 7 位

九、既然分支已经合并，分支就可以删除了

```
$ git branch -d feature1
Deleted branch feature1 (was 1ce6a6a).
```

### 分支管理

git 默认使用的是 **Fast forward** 模式，这个模式 master 分支在使用 `git log` 进行历史 commit 查看时，看不到合并时的 commit 信息。如果禁用 **Fast forward** 模式，git 在进行 `git merge` 时会生成一个 commit。

一、创建一个分支

```
$ git checkout -b dev
Switched to a new branch 'dev'
```

二、修改 readme.txt，并进行提交

```
$ git add readme.txt
$ git commit -m "and merge"
[dev 8f67fed] and merge
 1 file changed, 1 insertion(+)
```

三、切换回 master 分支

```
$ git checkout master
Switched to branch 'master'
```

四、进行合并，这也是和上面提到的各种分支操作中唯一不同的地方，使用了 `--no-ff` 参数

```
$ git merge --no-ff -m "merge with no-ff" dev
Merge made by the 'recursive' strategy.
 readme.txt | 1 +
 1 file changed, 1 insertion(+)
```

> `--no-ff` 表示不使用 **Fast forward** 模式 

五、查看 commit 历史

```
$ git log --graph --pretty=oneline --abbrev-commit
*   d522ccf merge with no-ff
|\  
| * 8f67fed and merge
|/  
* 0ff365e conflict fixed
*   d13db4c conflict fixed
|\  
| * 1ce6a6a AND Simple
* | 6f866ce & simple
|/  
* 8120b4d branch test
* 24b0dfb remove test.txt
* c035f16 add test.text
* 846a000 append GPL
* bdf9991 add distributed
* 3afae67 wrote a readme file
```

### 分支策略

在实际工作中，要好好借鉴下这章内容。  

首先 master 分支应该是最稳定的，是用来进行发布新版本的，平时开发工作不应该在此基础上开发。 
 
项目中应该还有一个研发分支 dev，dev 分支是相当于是一个研发测试分支，相对于 master 是不稳定，当要发布新版本时，再将 dev 中稳定的功能合并进 master 分支。  

研发人员都在 dev 分支上干活，同时每个人都有自己的分支，时不时的将自己分支内容合并进 dev 分支

### bug 分支

看完这章，感觉还有很多不明白......   

什么是 bug 分支？  

想象这么一个场景，你正在开心的写着代码，已经进行了50%，想着今天下班前就能提交这个 feature 了......但是就如那句话“我猜中了这个故事的开始，我将上帝赋予我的一切都奉献给了你。但我却没能料到这个故事的结局”。  

boss 突然降临，随手将一个 bug 任务，代号“bug-110”放到了你的桌上，外带一句话“立刻解决，下班前给我”。此时内心真是一万只草泥马在狂奔。正当你准备切换回 master 分支，创建 **issus-100** 来解决 bug。  

等下，会不会有问题？我手头的代码怎么处理？还没有完成，不能就这么提交 dev 分支吧？解决这个问题的一个思路就是使用 `git stash` 命令，将现有工作空间“存储”起来。  

在使用 `git stash` 命令之前，先查看下当前工作区状态  

```
$ git status
On branch dev
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	modified:   readme.txt

no changes added to commit (use "git add" and/or "git commit -a")
```

提示有一个文件 readme.txt 待处理。下面使用 `git stash` 命令

```
$ git stash
Saved working directory and index state WIP on dev: 8f67fed and merge
HEAD is now at 8f67fed and merge
```

现在再查看工作区状态

```
$ git status
On branch dev
nothing to commit, working directory clean
```

虽然没有进行过提交，但是工作区已经干净了。  

好了，现在就可以放心大胆的去解决 “bug-100”。先确定这个 bug 是在哪个分支上出现的，加入是 master 分支吧。好，那就先切换回 master 分支

```
$ git checkout master
```

创建 bug 分支——issue-100

```
$ git checkout -b issus-100
```

开始坑吃吃发现问题，追踪问题，解决问题......解决了，合并代码。首先，切换回 master

```
$ git checkout master
```

合并 `issus-100` 分支代码

```
$ git merge --no-ff -m "merged bug fix 100" issue-100
```

好了，bug 已解决。删除 bug 分支

```
$ git branch -d issus-100
```

看看时间，离下班还有一会，码农都是勤劳的蜜蜂，那就切换回 dev 分支继续咱的“事业”吧

```
$ git checkout dev
```

这时候问题来了，我之前的开发状态怎么还原呢？这时候的工作区可是 **clean**。使用 `git stash list` 命令

```
$ git stash list
stash@{0}: WIP on dev: 8f67fed and merge
```

这个列表就是之前用 `git stash` 储存的一系列内容。对于恢复这一内容，有两种方法：  

1. 使用 `git stash apply` 命令恢复，只是恢复以后，stash 内容还会存在，要使用 `git stash drop` 来删除  
2. 使用 `git stash pop` 命令来恢复，和 `git stash apply` 不同之处在于，`git stash pop` 在恢复状态的同时会把 stash 中对应的内容删除

```
$ git stash list
stash@{0}: WIP on dev: 8f67fed and merge
remote-mba:learngit remote$ git stash pop
On branch dev
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	modified:   readme.txt

no changes added to commit (use "git add" and/or "git commit -a")
Dropped refs/stash@{0} (8513524013bc1914d0d4e65143d0c9fa994941dc)
remote-mba:learngit remote$ 
```

再使用 `git stash list` 查看，就没有 stash 内容了。  

当然，你可以多次 `git stash` 进行状态存储，然后使用 `git stash list` 查看当前 stash 内容

```
$ git stash list
stash@{0}: WIP on dev: 8f67fed and merge
stash@{1}: WIP on dev: 8f67fed and merge
stash@{2}: WIP on dev: 8f67fed and merge
```

在其中选择想要恢复的状态

```
$ git stash apply stash@{1}
```

> 从状态名字看来，这个 stash 方法不能在同一时刻重复使用，谁能记住这0、1、2......都代表了什么内容？临时存储，恢复就删，应该这才是他的使用场景  

当 stash 中有很多内容，使用 `git stash pop` 只会获取内容中的第一个。

#### 疑问
1. 如果 issus-100 分支修改的 bug，正好在你开发分支上也存在，要怎么办？再修改一遍？亦或者，当你重新切换回 dev 分支—— `git checkout dev`，将 master 合并到 dev 分支 `git merge master`  

### feature 分支

基本情景类似于 bug 分支。但是当这个 feature-venus 分支即将合并进 dev 分支时，boss 决定这个功能拿掉

```
$ git branch -d feature-venus
error: The branch 'feature-venus' is not fully merged.
If you are sure you want to delete it, run 'git branch -D feature-venus'.
```

强行删除

```
$ git branch -D feature-venus
Deleted branch feature-venus (was 756d4af).
```

### 疑问
1. 这么一删除，不就等于功能白开发了？不能留着这个分支，但是不合并吗？看了后头的评论明白了，不删除分支是没有任何后果的，就是看起来乱。
2. 如果是已经合并后的功能点要拿掉怎么处理？使用 `git reset` 将 dev 分支回退，然后再删除 feature 分支即可

协同
---

在《添加远程库》章节说到本地版本库与远程版本库对应的问题，远程库在那一章节里叫做 origin。

查看远程相信信息

```
$ git remote -v
origin	git@github.com:remotesc2/learngit.git (fetch)
origin	git@github.com:remotesc2/learngit.git (push)
```

> fetch —— 抓取地址  
> push ——推送地址

### 推送分支

```
$ git push origin dev
```

至于什么推送，什么可以据为己有？

+ master 分支是主分支，必须时刻与远程同步。
+ dev 分支是开发分支，团队所有成员都需要在上面工作，必须与远程同步。
+ bug 分支只用于在本地修复bug，就没必要推到远程了，除非 boss 要看看你每周到底修复了几个bug。
+ feature 分支是否推到远程，取决于是否需要合作开发。

### 抓取分支

使用 `git clone` 抓取远程库

```
$ git clone git@github.com:remotesc2/gitskills.git

Cloning into 'gitskills'...
remote: Counting objects: 3, done.
remote: Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
Receiving objects: 100% (3/3), done.
Checking connectivity... done.
```

一般情况下，这时候 clone 下来的只有 master 分支，即远程库中的默认分支，这是可以设置的。可以使用 `git branch` 来查看本地库有几个分支。  

如果需要 dev 分支进行开发工作，那就需要将远程 origin 的 dev 分支创建到本地

```
$ git checkout -b dev origin/dev

fatal: Cannot update paths and switch to branch 'dev' at the same time.
Did you intend to checkout 'origin/dev' which can not be resolved as commit?
```

> 出错了......

现在他就可以进行开发了

1. `git add test.js`
2. `git commit -m "add something"`
3. `git push -u origin dev` or `git push`

那么问题来了，如果你要 push 的代码，他之前正巧刚 push 过，显而易见“冲突”了

```
$ git push

To git@github.com:remotesc2/gitskills.git
 ! [rejected]        master -> master (fetch first)
error: failed to push some refs to 'git@github.com:remotesc2/gitskills.git'
hint: Updates were rejected because the remote contains work that you do
hint: not have locally. This is usually caused by another repository pushing
hint: to the same ref. You may want to first integrate the remote changes
hint: (e.g., 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
```

只能按照 git 提示，先用 `git pull` 把最新的提交从 origin/dev 抓下来，然后在本地合并，解决冲突，再推送

```
$ git pull

remote: Counting objects: 3, done.
remote: Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
Unpacking objects: 100% (3/3), done.
From github.com:remotesc2/gitskills
   68384e1..1460acc  master     -> origin/master
 * [new branch]      dev        -> origin/dev
Auto-merging README.md
CONFLICT (content): Merge conflict in README.md
Automatic merge failed; fix conflicts and then commit the result.
```

后面和“分支冲突”章节过程一样了，解决完冲突，最后提交

```
$ git add README.md
$ git commit -m "merge & fix README.md"

[master 4d5b3f0] merge & fix README.md
remote-mba:gitskills remote$ git push
Counting objects: 4, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (2/2), done.
Writing objects: 100% (4/4), 411 bytes | 0 bytes/s, done.
Total 4 (delta 1), reused 0 (delta 0)
To git@github.com:remotesc2/gitskills.git
   1460acc..4d5b3f0  master -> master
```

标签
---

标签说白了就是对于 commit id 的一个别名。

### 新建标签 & 查找标签

切换到某个分支，比如 master，现在这次 commit 之后就是所谓的 1.0 版本了，这时候就需要打个标签

```
$ git tag v1.0
```

使用 `git tag` 可以查看所有标签

```
$ git tag
v1.0
```

如果过想对过往的某个 commit 进行打标签，可以先查看 commit 历史

```
$ git log --pretty=oneline --abbrev-commit

8f67fed and merge
0ff365e conflict fixed
d13db4c conflict fixed
6f866ce & simple
1ce6a6a AND Simple
8120b4d branch test
24b0dfb remove test.txt
c035f16 add test.text
846a000 append GPL
bdf9991 add distributed
3afae67 wrote a readme file
```

找到需要打标签的 commit，比如就是 6f866ce

```
$ git tag v0.9 0ff365e
```

然后查看所有标签

```
$ git tag
v0.9
v1.0
```

> 标签不是按时间顺序列出，而是按字母排序的

使用 `git show <tagname>` 查看具体某个标签信息

```
$ git show v1.0
commit 8f67fedf4d332942c497411ee259d8f9070b56dc
Author: remotesc2 <remotesc2@foxmail.com>
Date:   Sat Jun 25 10:50:42 2016 +0800

    and merge

diff --git a/readme.txt b/readme.txt
index 2d582fc..cb5ead3 100644
--- a/readme.txt
+++ b/readme.txt
@@ -2,3 +2,4 @@ Git is a distributed version control system.
 Git is free software distributed under the GPL.
 Git has a mutable index called stage.
 Creating a new branch is quick & simple.
+add merge
```

> tag 展示的就是相对应 commit 信息

还可以创建带有说明的标签，`-a` 指定标签，`-m` 指定说明文字

```
$ git tag -a v0.1 -m "version 0.1 released" c035f16
```

使用 `git show <tagname>` 查看

```
$ git show v0.1
tag v0.1
Tagger: remotesc2 <remotesc2@foxmail.com>
Date:   Sat Jun 25 15:07:47 2016 +0800

version 0.1 released

commit c035f162da2f5746dfb365fa98aa4a4bdd20583b
Author: remotesc2 <remotesc2@foxmail.com>
Date:   Fri Jun 24 23:23:34 2016 +0800

    add test.text

diff --git a/test.txt b/test.txt
new file mode 100644
index 0000000..e69de29
```

### 标签推送远程主机

推送指定标签

```
$ git push origin v1.0
```

一次性推送所有未推送标签

```
$ git push origin --tags
```

### 删除标签

针对本地标签

```
$ git tag -d v0.1
Deleted tag 'v0.1' (was 7b1203d)
```

如果标签已经推送到了远程主机

先删除本地标签

```
$ git tag -d v0.1
Deleted tag 'v0.1' (was 7b1203d)
```

然后再远程删除标签，也是使用 `git push` 命令

```
$ git push origin :refs/tags/v0.1
To git@github.com:remotesc2/learngit.git
 - [deleted]         v0.1
```

忽略文件 .gitignore
---

如果需要检查 .gitignore 文件是否有问题，可以使用 `git check-ignore` 命令
如果有些文件已经被纳入了 .gitignore 忽略列表，但是又必须进行上传，可以使用 `git add` 的 **-f** 属性

```
$ git add -f specialFile.txt
```

配置文件 .git/config
---

可以对自己经常使用的 git 命令添加别名，例如 `git status` 可以缩写成 `git su`

```
$ git config alias.st status
```

> config 有个 **--global** 属性，如果添加上了就是全局配置，如果没有就是只对当前 git 生效

所有对当前 git 的配置都存放在 .git/config 文件内

最后，自己搭建 git 服务器
---

算了，我还是直接 GitHub 好了，暂时来说，自己搭建意义不大......  
但是还是把方法记录下来，万一以后想开了呢？

首先，先整一套 Ubuntu。  

### 安装 git

```
$ sudo apt-get install git
```

### 创建 git 用户，用来运行 git 服务

```
$ sudo adduser git
```

### 收集登陆证书

收集所有需要登录的用户公钥，导入到服务器的 **/home/git/.ssh/authorized_keys** 文件内，一行一个

### 初始化 git 库

创建一个文件夹作为 git 库，假设是 **srv/sample.git**，在 **srv/** 目录下执行如下命令

```
$ sudo git init --bare sample.git
```

> 创建le 一个裸仓库，裸仓库没有工作区，因为服务器上的 git 仓库纯粹是为了共享，所以不让用户直接登录到服务器上去改工作区，并且服务器上的 git 仓库通常都以 .git 结尾

然后，把 owner 改为 git

```
$ sudo chown -R git:git sample.git
```

### 禁止 shell 登录

出于安全考虑，第二步创建的git用户不允许登录shell，这可以通过编辑/etc/passwd文件完成。找到类似下面的一行

```
git:x:1001:1001:,,,:/home/git:/bin/bash
```

改成

```
git:x:1001:1001:,,,:/home/git:/usr/bin/git-shell
```

这样，git 用户可以正常通过 ssh 使用 git，但无法登录 shell，因为我们为 git 用户指定的 git-shell 每次一登录就自动退出。

> 表示这块看不懂了，这个 sheel 登录是个什么？好大的坑！

### 克隆远程库

现在就可以通过 `git clone` 命令克隆远程仓库了

```
$ git clone git@server:/srv/sample.git
Cloning into 'sample'...
warning: You appear to have cloned an empty repository.
```

想想
---

花了十几个小时，把 git 的基本教程粗略过了一遍，谢谢廖雪峰的教程。我这个知识圈里看来还有太多窟窿要去填充。  

想想这几天，从 **react.js** 到 **node.js & express**，从 **GitHub & Hexo** 到 **git**，从 **post请求 到 双钥加密**，不禁一声叹息——怎么那么多。 

参考
---
1. [http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/00137586810169600f39e17409a4358b1ac0d3621356287000](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/00137586810169600f39e17409a4358b1ac0d3621356287000)
2. [http://www.yiibai.com/git/git_push.html](http://www.yiibai.com/git/git_push.html)

