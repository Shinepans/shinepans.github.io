---
layout: post
title:  狂刷Git技能
categories: others
---

关于Git的技能，网上的教程多不胜数。关于Git和SVN的异同，网上也是遍地都是。厌倦了SVN的分支的操作之后，我们团队决定做一次变革，以后的所有的项目都使用Git来做为版本控制工具。所以，在这里记录一下常用的Git操作。

## 万能命令 ##

不解释，在程序员眼里一个命令的help就是万能的。

```shell
git help
git help -a
git help -g
```


## 基础操作 ##

初始化git仓库 ，命令：```git init```

添加文件到暂存区，命令：```git add README.md```

提交文件到分支，命令：```git commit -m "提交README.md文件"```

**问题1：修改了文件还没有使用```git add```提交，怎么还原？**

首先使用```git status```查看状态，然后更具状态去选择性操作。

```shell
# 查看状态
git status
# 命令结果显示
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	modified:   README.md

no changes added to commit (use "git add" and/or "git commit -a")
```

看上面的说明，这个时候你可以选择添加到缓存区，或者选择还原文件。还原的命令为```git checkout -- 文件名```。

下面我们还原文件。

```shell
git checkout -- README.md
```

这个时候，文件README.md就恢复到了修改前的状态。

**问题2：修改了文件已经使用```git add```添加，但是还没有使用```git commit```提交怎么还原？**

还是和刚才一样，我们首先看一下```git add```了之后的```git status```。

```shell
# 查看状态
git status
# 命令结果显示
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

	modified:   README.md
```

看上面的说明，这个时候你可以选择```git reset HEAD 文件名```的方式进行文件的还原。或者提交到版本库里面。

下面我们还原文件。

```shell
git reset HEAD README.md
```

这个时候，文件README.md也同样恢复到了修改前的状态。

看到这里，动动脚趾头我们就会知道问题3是什么了。问题3如下。

**问题3：修改了文件已经使用```git add```添加到暂存区，并且还是用了```git commit```提交到了版本库之中，我们该怎么还原？**

还是同样的办法。看看已经修改过的文件经过了```git add```, ```git commit```之后的```git status```是什么。

```shell
# 查看状态
git status
# 命令结果显示
On branch master
nothing to commit, working directory clean
```

看到上面的提示是不是大吃一惊，竟然说工作区很干净，没有任何内容需要提交。并且没有任何回退的操作的提示。这个时候不要急，我们要知道任何东西都会留下点痕迹的。我们的解决思路是这样的：找到提交的日志，通过提交的日志进行恢复。

```
# 查看日志
git log
# 命令结果显示。 注意：具体内容依据你的客户端和提交的注释。
commit a2bba85f5bcdfca4186a03c27e68cb06e244951f
Author: searchpcc <searchpcc@163.com>
Date:   Tue Aug 11 22:58:41 2015 +0800

    第二次提交README.md

commit 6c87b3f956066d418b969f7e4429d93aedec73c2
Author: searchpcc <searchpcc@163.com>
Date:   Tue Aug 11 22:58:18 2015 +0800

    第一次提交README.md
```

我们需要回退到第一次提交README.md后的状态。我们可以使用 ```git reset --hard commitId(前几位就行)```。当然，事实上有了这条命令你可以恢复到之前的任何一个commit。

```shell
# 命令
git reset --hard 6c87b3f95
# 显示
HEAD is now at 6c87b3f 第一次提交README.md
```

有的人，特别是初学版本控制的人以为到这里问题系列就结束了。未add的回退，add了为commit的回退，add了且commit了的回退。事实上还有一种情况就是你回到了过去，如果再回来呢？比如我从版本三回到了版本一，然后发现版本一不是我想回到的地方，我该怎么重新回到版本三呢？

**问题4：修改了文件已经使用git add 提交，并且还是用了```git commit```提交到了版本库之中，我们该怎么还原？**

解决的思路和问题3类似，查看日志，然后会退到相应的版本。但是，注意这次查看日志的方式不太一样，使用的是```git reflog```。

```shell
# 查看日志
git reflog
# 命令结果显示。 注意：具体内容依据你的客户端和提交的注释。
6c87b3f HEAD@{0}: reset: moving to 6c87b3f956066d418b969f7e4429d93aedec73c2
a2bba85 HEAD@{1}: commit: 第二次提交README.md
6c87b3f HEAD@{2}: commit (initial): 第一次提交README.md
```

看到版本ID，然后进行回退。

```shell
# 命令
get reset --hard a2bba85
# 显示
HEAD is now at a2bba85 第二次提交README.md
```

到这里，上面所有的功能其实SVN都是有的，并且实现的难度都差不多。可能有些人有些许着急了，这不是Git的一篇狂刷技能么？怎么还不涉及点干货呢？下面就来干货。还是沿袭之前的问问题的方式。

**问题5：如何创建并切换分支？**

这个问题比较简单。如果想要一条命令实现的话直接输入下面这条命令就可以创建并且切换到dev分支。

```
# 创建并切换
git checkout -b dev
```

如果想多体验一下敲键盘的快感的话。也可以使用如下两条命令。

```
# 创建
git branch dev
# 切换
git  checkout dev
```

补充几个：

```git branch``` 查看当前分支。其那面有```*```的代表当前分支。

```git branch -d```删除分支。注意删除分支是有一些条件的哦。

```git checkout 分支名称```切换分支。

**问题6：如何合并分支并且解决冲突呢？**

这个问题最关键。其实SVN最让人诟病的有两点。一点是集中式处理方式而不是分布式，另一点就是无论是合并还是创建分支的速度都是巨慢。

合并分支。比如把dev合并到master之上，你需要先把dev的分支的内容add并且commit，然后切换到master。在master之上使用```git merge dev```，如果没有冲突就算合并好了。

```
# 创建并切换分支
git checkout -b dev
# 修改分支上的文件
......
# 切回主分支
git checkout master
# 合并分支
git merge dev
```

解决冲突的唯一的办法就是信息的看冲突的地方。然后删除，修改......其他的没有捷径。下面看一个简单地冲突吧。

```shell
# 合并爆冲突
Auto-merging README.md
CONFLICT (content): Merge conflict in README.md
Automatic merge failed; fix conflicts and then commit the result.
```

使用```git diff```比较文件

```shell
diff --cc README.md
index fdd5d55,9826dcc..0000000
--- a/README.md
+++ b/README.md
@@@ -1,2 -1,2 +1,6 @@@
++<<<<<<< HEAD
 +134234hello world
++=======
+ 112212hello world
++>>>>>>> dev
  hello world
```

上面的结果很显然是你当前改的地方和dev冲突了一行。然后这行的内容是......

## 远程操作 ##

远程操作就是在前面的操作结束之后把本地的代码push到远程服务器端。使用的命令是```git push```。当然，前提是你先得连接到服务器。比如先从服务器```git clone```一份代码下来。

常用的命令有：

```
git clone 地址
# 推送到远程主干
git push origin master
等等
```

## 小结 ##

有了SVN的基础学习Git也就很容易了。只要明白分布式的特点就OK了。其他的命令看帮助，或者Google It。最后mac上推荐一个工具[SourceTree]，用了的都说好。

## 致谢 ##

感谢下面这些站点的作者，这些站点给了我很多思路。

网站1： [Git教程]

网站2： [Github]

（全文完）

[Git教程]:http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000
[Github]:https://www.github.com
[SourceTree]:https://www.sourcetreeapp.com/








