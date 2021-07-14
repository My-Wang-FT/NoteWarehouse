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

工作区（Working directory）：我们的Git项目存在的目录

版本库（Repository）：工作区有一个隐藏目录`.git`，这个不算工作区，而是Git的版本库。

Git的版本库里存了很多东西，其中最重要的就是称为stage（或者叫index）的暂存区，还有Git为我们自动创建的第一个分支`master`，以及指向`master`的一个指针叫`HEAD`。

![0](./Pictures/0.jpeg)

第一步是用`git add`把文件添加进去，实际上就是把文件修改添加到暂存区；

第二步是用`git commit`提交更改，实际上就是把暂存区的所有内容提交到当前分支。

因为我们创建Git版本库时，Git自动为我们创建了唯一一个`master`分支，所以，现在，`git commit`就是往`master`分支上提交更改。

即需要提交的文件修改通通放到暂存区，然后，一次性提交暂存区的所有修改。

### 撤销修改

如果对某个文件的修改不满意，想要撤销之前的全部修改，则可以通过使用`git checkout -- filename`来丢弃工作区的修改，**注意**一定要加`--`，否则会变为另一个指令

`git checkout`其实是用版本库里的版本替换工作区的版本，无论工作区是修改还是删除，都可以“一键还原”。

命令`git checkout -- readme.txt`意思就是，把`readme.txt`文件在工作区的修改全部撤销，这里有两种情况：

一种是`readme.txt`自修改后还没有被放到暂存区，现在，撤销修改就回到和版本库一模一样的状态；

一种是`readme.txt`已经添加到暂存区后，又作了修改，现在，撤销修改就回到添加到暂存区后的状态。

总之，就是让这个文件回到最近一次`git commit`或`git add`时的状态。

场景1：当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，用命令`git checkout -- file`。

场景2：当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，第一步用命令`git reset HEAD <file>`，就回到了场景1，第二步按场景1操作。

场景3：已经提交了不合适的修改到版本库时，想要撤销本次提交，参考版本回退一节，不过前提是没有推送到远程库。

### 删除文件

在删除本地文件的时候，`git status`会告诉你哪些文件被删除了。接下来有两种操作：

一种是真的需要删除该文件，那就用命令`git rm`删掉，并且`git commit`上传版本。

另一种情况是删错了，因为版本库里还有呢，所以可以很轻松地把误删的文件恢复到最新版本`git checkout -- filename`

## 远程仓库

由于你的本地Git仓库和GitHub仓库之间的传输是通过SSH加密的，所以，需要一点设置：

第1步：创建SSH Key。在用户主目录下，看看有没有.ssh目录，如果有，再看看这个目录下有没有`id_rsa`和`id_rsa.pub`这两个文件，如果已经有了，可直接跳到下一步。如果没有，打开Shell（Windows下打开Git Bash），创建SSH Key：

```
$ ssh-keygen -t rsa -C "youremail@example.com"
```

你需要把邮件地址换成你自己的邮件地址，然后一路回车，使用默认值即可，由于这个Key也不是用于军事目的，所以也无需设置密码。

如果一切顺利的话，可以在用户主目录里找到`.ssh`目录，里面有`id_rsa`和`id_rsa.pub`两个文件，这两个就是SSH Key的秘钥对，`id_rsa`是私钥，不能泄露出去，`id_rsa.pub`是公钥，可以放心地告诉任何人。

第2步：登陆GitHub，打开“Account settings”，“SSH Keys”页面：

然后，点“Add SSH Key”，填上任意Title，在Key文本框里粘贴`id_rsa.pub`文件的内容

点“Add Key”，你就应该看到已经添加的Key

当然，GitHub允许你添加多个Key。假定你有若干电脑，你一会儿在公司提交，一会儿在家里提交，只要把每台电脑的Key都添加到GitHub，就可以在每台电脑上往GitHub推送了。

### 添加远程仓库

首先需要在GitHub上创建与本地工作区同名的仓库，我们首先要将该仓库与本地已有仓库关联到一起：

在本地的`learngit`仓库下运行命令：

```
$ git remote add origin git@github.com:michaelliao/learngit.git
```

请千万注意，把上面的`michaelliao`替换成你自己的GitHub账户名，否则，你在本地关联的就是我的远程库，关联没有问题，但是你以后推送是推不上去的，因为你的SSH Key公钥不在我的账户列表中。

添加后，远程库的名字就是`origin`，这是Git默认的叫法，也可以改成别的，但是`origin`这个名字一看就知道是远程库。

下一步，就可以把本地库的所有内容推送到远程库上：

```shell
$ git push -u origin master
```

把本地库的内容推送到远程，用`git push`命令，实际上是把当前分支`master`推送到远程。

我们第一次推送`master`分支时，加上了`-u`参数，Git不但会把本地的`master`分支内容推送的远程新的`master`分支，还会把本地的`master`分支和远程的`master`分支关联起来，在以后的推送或者拉取时就可以简化命令。

从现在起，只要本地作了提交，就可以通过命令：

```shell
$ git push origin master
```

把本地`master`分支的最新修改推送至GitHub，现在，你就拥有了真正的分布式版本库！

### 删除远程库

如果添加的时候地址写错了，或者就是想删除远程库，可以用`git remote rm <name>`命令。使用前，建议先用`git remote -v`查看远程库信息：

```shell
$ git remote -v
origin  git@github.com:michaelliao/learn-git.git (fetch)
origin  git@github.com:michaelliao/learn-git.git (push)
```

然后，根据名字删除，比如删除`origin`：

```shell
$ git remote rm origin
```

此处的“删除”其实是解除了本地和远程的绑定关系，并不是物理上删除了远程库。远程库本身并没有任何改动。要真正删除远程库，需要登录到GitHub，在后台页面找到删除按钮再删除。

### 从远程仓库克隆



## 分支管理

### 创建与合并分支



### 解决冲突



### 分支管理策略



### Bug分支



### Feature分支



### 多人协作



### Rebase