# Git学习笔记

本笔记学习内容来源：[廖雪峰的Git教程](https://www.liaoxuefeng.com/wiki/896043488029600) 

## 本地Git使用

###  创建版本库

版本库：版本库又名仓库，英文名**repository**，你可以简单理解成一个目录，这个目录里面的所有文件都可以被Git管理起来，每个文件的修改、删除，Git都能跟踪，以便任何时刻都可以追踪历史，或者在将来某个时刻可以“还原”。

```shell
$ mkdir learngit
$ cd learngit
$ git init
```

创建文件之后，需要将文件放入仓库中

1. 用`git add filename`命令告诉Git，把文件加入仓库，不会有任何提示
2. 用命令`git commit`告诉Git，把文件提交到仓库

```shell
$ git commit -m "wrote a readme file"
```

简单解释一下`git commit`命令，`-m`后面输入的是本次提交的说明，可以输入任意内容，当然最好是有意义的，这样你就能从历史记录里方便地找到改动记录。

备注：`git commit`可以一次性提交多个文件，比如

```shell
$ git add file1.txt
$ git add file2.txt file3.txt
$ git commit -m "add 3 files."
```

### 文件修改

若在本地修改了某个文件，则可以使用`git status`查看结果

比如若修改了readme.txt，会有如下提示：

```shell
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	modified:   readme.txt

no changes added to commit (use "git add" and/or "git commit -a")
```

如果需要查看具体修改内容，需要使用命令`git diff filename`

```shell
$ git diff readme.txt 
diff --git a/readme.txt b/readme.txt
index 46d49bf..9247db6 100644
--- a/readme.txt
+++ b/readme.txt
@@ -1,2 +1,2 @@
-Git is a version control system.
+Git is a distributed version control system.
 Git is free software.
```

### 版本回退

查看之前的Git日志，即之前的修改记录`git log`

```shell
commit 82390482e3026e7322b5a5f189898561ad73d073 (HEAD -> master)
Author: mywang <wmyang806@outlook.com>
Date:   Wed Jul 14 19:19:39 2021 +0800

    add GPL

commit 291a7de2bbc1780fac0a4d814b1690d992eb81bc
Author: mywang <wmyang806@outlook.com>
Date:   Wed Jul 14 19:17:03 2021 +0800

    update readme file

commit ef02d64544cc03908c56e61aa16780b5c8c06797
Author: mywang <wmyang806@outlook.com>
Date:   Wed Jul 14 19:01:11 2021 +0800

    write a readme file
```

解释：commit 后面的字符是版本号`commit_id`，之后是更改的作者和邮箱，之后是修改的日期，最后是commit时增加的描述

其中如果不需要显示这么复杂，则可以增加参数 `--pretty=<tab>`来修改显示格式

Git的当前版本用`HEAD`描述，向上一个版本，则使用`HEAD^`，向上100个版本则使用`HEAD~100`，事实上，`HEAD`是一个指针，指向当前版本的`commit_id`，如果进行版本回退，Git会将指针指向新的版本号，然后顺便把工作区的文件更新了。

如果要回退到之前的某个版本，需要使用`git reset`命令

```shell
$ git reset --hard HEAD^ 
HEAD 现在位于 291a7de update readme file
```

或者使用版本号（不用写全，Git会自动寻找对应的版本号）

```shell
$ git reset --hard 8239    
HEAD 现在位于 8239048 add GPL
```

此时`git log`的内容将会回退到上一个版本，不会有新一个版本的内容记录

因此，如果需要回到新的版本，必须要知道新的版本的`commit_id`，可以使用`git reflog`来查看之前

```shell
$ git reflog
291a7de (HEAD -> master) HEAD@{0}: reset: moving to HEAD^
8239048 HEAD@{1}: reset: moving to 8239
291a7de (HEAD -> master) HEAD@{2}: reset: moving to HEAD^
8239048 HEAD@{3}: commit: add GPL
291a7de (HEAD -> master) HEAD@{4}: commit: update readme file
ef02d64 HEAD@{5}: commit (initial): write a readme file
```

上面的语句中，带有`(HEAD -> master)`的是当前版本，前面的是对应版本号，整个日志记录了从最开始创建文件，到现在全部的修改内容，当然也包括版本号，因此可以通过这个语句将文件更新到任意版本。





### 工作区和暂存区



### 管理修改



### 撤销修改



### 删除文件



## 远程仓库

### 添加远程仓库



### 从远程仓库克隆



## 分支管理

### 创建与合并分支



### 解决冲突



### 分支管理策略



### Bug分支



### Feature分支



### 多人协作



### Rebase