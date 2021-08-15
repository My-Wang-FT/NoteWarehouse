# Git 学习笔记

本笔记学习内容来源：[廖雪峰的 Git 教程](https://www.liaoxuefeng.com/wiki/896043488029600)

## 课代表指令总结

1. Git 基本操作
   - `git init`：新建本地仓库需要初始化
   - `git status`：查看当前工作区文件状态
   - `git add <filename>`：向暂存区添加修改过的文件
   - `git rm <filename>`：将本来在版本里的文件删除，适用于真的需要删除文件的情况
   - `git diff <filename>`：查看某文件的修改记录
   - `git commit -m "<destription>"`：将暂存区的修改提交到 Git 形成一个新的版本，附带描述
   - `git log`：查看当前版本前的全部提交版本信息
   - `git reflog`：查看所有的版本提交历史记录，用于查看版本号
   - `git reset --hard HEAD^ `：会退到上一个版本
   - `git reset --hard <commit_id> `：回到指定版本，版本号不需要全部，能区分即可
   - `git checkout -- <filename>`：把文件在工作区的修改全部撤销，甚至可以恢复删除的文件，适用于还没有`add`的情况
   - `git reset HEAD <filename>`：将已经`add`到暂存区的某文件去除，回退到当前版本没有被`add`但是被修改了的修改状态
2. Git 远程仓库
   - `git remote add origin git@github.com:michaelliao/learngit.git`：关联 GitHub 远程仓库的 Git 项目
   - `git push -u origin master`：将当前的`master`分支提交到远程仓库中，第一次需要加上`-u`指令进行关联
   - `git pull`：抓取远程的新提交，如果有冲突，需要先处理冲突
   - `git remote -v`：查看远程版本库信息
   - `git remote rm origin`：删除本地库与远程库的联系，当然本地库与远程库都还在
   - `git clone git@github.com:Username/Projectname.git`：用 ssh 协议克隆远程仓库到本地
   - `git clone https://github.com/Username/Projectname.git`：用 http 协议克隆远程仓库到本地
   - `git checkout -b branch-name origin/branch-name`：在本地创建和远程分支对应的分支，本地分支最好和远程分支一致
   - `git branch --set-upstream branch-name origin/branch-name`
3. Git 分支管理
   - `git branch`：查看分支
   - `git branch <name>`：创建分支
   - `git checkout <name>` & `git switch <name>`：切换分支
   - `git checkout -b <name>` & `git switch -c <name>`：创建并切换分支
   - `git merge <name>`：合并某分支到当前分支
   - `git branch -d <name>`：删除分支
   - `git branch -D <name>`：强行删除没有被合并的分支
   - `git stash`：暂存目前的工作现场，但是并不提交为新版本
   - `git stash list`：查看多个 stash 内容
   - `git stash pop`：将最新暂存的工作恢复，并删除这个 stash
   - `git stash apply`：恢复最新的工作内容，同事不删除这个 stash
   - `git stash apply stash@{0}`：恢复指定版本的 stash
   - `git cherry-pick <commit>`：将在以前分支上做的修改应用到当前分支，注意只有修改内容
4. 标签管理
   * `git tag <tagname>`：用于新建一个标签，默认为`HEAD`，也可以指定一个`commit id`
   * `git tag -a <tagname> -m "blablabla..."`：可以指定标签信息
   * `git tag`：可以查看所有标签
   * `git push origin <tagname>`：可以推送一个本地标签
   * `git push origin --tags`：可以推送全部未推送过的本地标签
   * `git tag -d <tagname>`：可以删除一个本地标签
   * `git push origin :refs/tags/<tagname>`：可以删除一个远程标签

## 本地 Git 使用

### 创建版本库

版本库：版本库又名仓库，英文名**repository**，你可以简单理解成一个目录，这个目录里面的所有文件都可以被 Git 管理起来，每个文件的修改、删除，Git 都能跟踪，以便任何时刻都可以追踪历史，或者在将来某个时刻可以“还原”。

```shell
$ mkdir learngit
$ cd learngit
$ git init
```

创建文件之后，需要将文件放入仓库中

1. 用`git add filename`命令告诉 Git，把文件加入仓库，不会有任何提示
2. 用命令`git commit`告诉 Git，把文件提交到仓库

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

比如若修改了 readme.txt，会有如下提示：

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

查看之前的 Git 日志，即之前的修改记录`git log`

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

解释：commit 后面的字符是版本号`commit_id`，之后是更改的作者和邮箱，之后是修改的日期，最后是 commit 时增加的描述

其中如果不需要显示这么复杂，则可以增加参数 `--pretty=<tab>`来修改显示格式

Git 的当前版本用`HEAD`描述，向上一个版本，则使用`HEAD^`，向上 100 个版本则使用`HEAD~100`，事实上，`HEAD`是一个指针，指向当前版本的`commit_id`，如果进行版本回退，Git 会将指针指向新的版本号，然后顺便把工作区的文件更新了。了解更多`HEAD`的实质，请看分支管理一节。

如果要回退到之前的某个版本，需要使用`git reset`命令

```shell
$ git reset --hard HEAD^
HEAD 现在位于 291a7de update readme file
```

或者使用版本号（不用写全，Git 会自动寻找对应的版本号）

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

工作区（Working directory）：我们的 Git 项目存在的目录

版本库（Repository）：工作区有一个隐藏目录`.git`，这个不算工作区，而是 Git 的版本库。

Git 的版本库里存了很多东西，其中最重要的就是称为 stage（或者叫 index）的暂存区，还有 Git 为我们自动创建的第一个分支`master`，以及指向`master`的一个指针叫`HEAD`。

![0](./Pictures/0.jpeg)

第一步是用`git add`把文件添加进去，实际上就是把文件修改添加到暂存区；

第二步是用`git commit`提交更改，实际上就是把暂存区的所有内容提交到当前分支。

因为我们创建 Git 版本库时，Git 自动为我们创建了唯一一个`master`分支，所以，现在，`git commit`就是往`master`分支上提交更改。

即需要提交的文件修改通通放到暂存区，然后，一次性提交暂存区的所有修改。

### 撤销修改

如果对某个文件的修改不满意，想要撤销之前的全部修改，则可以通过使用`git checkout -- filename`来丢弃工作区的修改，**注意**一定要加`--`，否则会变为另一个指令

`git checkout`其实是用版本库里的版本替换工作区的版本，无论工作区是修改还是删除，都可以“一键还原”。

命令`git checkout -- readme.txt`意思就是，把`readme.txt`文件在工作区的修改全部撤销，这里有两种情况：

一种是`readme.txt`自修改后还没有被放到暂存区，现在，撤销修改就回到和版本库一模一样的状态；

一种是`readme.txt`已经添加到暂存区后，又作了修改，现在，撤销修改就回到添加到暂存区后的状态。

总之，就是让这个文件回到最近一次`git commit`或`git add`时的状态。

场景 1：当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，用命令`git checkout -- file`。

场景 2：当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，第一步用命令`git reset HEAD <file>`，就回到了场景 1，第二步按场景 1 操作。

场景 3：已经提交了不合适的修改到版本库时，想要撤销本次提交，参考版本回退一节，不过前提是没有推送到远程库。

### 删除文件

在删除本地文件的时候，`git status`会告诉你哪些文件被删除了。接下来有两种操作：

一种是真的需要删除该文件，那就用命令`git rm`删掉，并且`git commit`上传版本。

另一种情况是删错了，因为版本库里还有呢，所以可以很轻松地把误删的文件恢复到最新版本`git checkout -- filename`

## 远程仓库

由于你的本地 Git 仓库和 GitHub 仓库之间的传输是通过 SSH 加密的，所以，需要一点设置：

第 1 步：创建 SSH Key。在用户主目录下，看看有没有.ssh 目录，如果有，再看看这个目录下有没有`id_rsa`和`id_rsa.pub`这两个文件，如果已经有了，可直接跳到下一步。如果没有，打开 Shell（Windows 下打开 Git Bash），创建 SSH Key：

```
$ ssh-keygen -t rsa -C "youremail@example.com"
```

你需要把邮件地址换成你自己的邮件地址，然后一路回车，使用默认值即可，由于这个 Key 也不是用于军事目的，所以也无需设置密码。

如果一切顺利的话，可以在用户主目录里找到`.ssh`目录，里面有`id_rsa`和`id_rsa.pub`两个文件，这两个就是 SSH Key 的秘钥对，`id_rsa`是私钥，不能泄露出去，`id_rsa.pub`是公钥，可以放心地告诉任何人。

第 2 步：登陆 GitHub，打开“Account settings”，“SSH Keys”页面：

然后，点“Add SSH Key”，填上任意 Title，在 Key 文本框里粘贴`id_rsa.pub`文件的内容

点“Add Key”，你就应该看到已经添加的 Key

当然，GitHub 允许你添加多个 Key。假定你有若干电脑，你一会儿在公司提交，一会儿在家里提交，只要把每台电脑的 Key 都添加到 GitHub，就可以在每台电脑上往 GitHub 推送了。

### 添加远程仓库

首先需要在 GitHub 上创建与本地工作区同名的仓库，我们首先要将该仓库与本地已有仓库关联到一起：

在本地的`learngit`仓库下运行命令：

```
$ git remote add origin git@github.com:michaelliao/learngit.git
```

请千万注意，把上面的`michaelliao`替换成你自己的 GitHub 账户名，否则，你在本地关联的就是我的远程库，关联没有问题，但是你以后推送是推不上去的，因为你的 SSH Key 公钥不在我的账户列表中。

添加后，远程库的名字就是`origin`，这是 Git 默认的叫法，也可以改成别的，但是`origin`这个名字一看就知道是远程库。

下一步，就可以把本地库的所有内容推送到远程库上：

```shell
$ git push -u origin master
```

把本地库的内容推送到远程，用`git push`命令，实际上是把当前分支`master`推送到远程。

我们第一次推送`master`分支时，加上了`-u`参数，Git 不但会把本地的`master`分支内容推送的远程新的`master`分支，还会把本地的`master`分支和远程的`master`分支关联起来，在以后的推送或者拉取时就可以简化命令。

从现在起，只要本地作了提交，就可以通过命令：

```shell
$ git push origin master
```

把本地`master`分支的最新修改推送至 GitHub，现在，你就拥有了真正的分布式版本库！

### 删除远程库

如果添加的时候地址写错了，或者就是想删除远程库，可以用`git remote rm <name>`命令。使用前，建议先用`git remote -v`查看远程库信息：

```shell
$ git remote -v
origin  git@github.com:michaelliao/learn-git.git (fetch)
origin  git@github.com:michaelliao/learn-git.git (push)
```

上面显示了可以抓取和推送的 origin 的地址。如果没有推送权限，就看不到 push 的地址。

然后，根据名字删除，比如删除`origin`：

```shell
$ git remote rm origin
```

此处的“删除”其实是解除了本地和远程的绑定关系，并不是物理上删除了远程库。远程库本身并没有任何改动。要真正删除远程库，需要登录到 GitHub，在后台页面找到删除按钮再删除。

### 从远程仓库克隆

使用 GitHub 中的指令即可克隆该库

GitHub 给出的地址不止一个，还可以用`https://github.com/michaelliao/gitskills.git`这样的地址。实际上，Git 支持多种协议，默认的`git://`使用 ssh，但也可以使用`https`等其他协议。

## 分支管理

在 Git 中，分支实际是使用指针实现的。每次当我们进行`commit`工作，都是让`HEAD`指向的当前分支指针指向最新的提交版本，而`HEAD`指向的是当前分支，当前分支指向的是版本，因此使用`HEAD`可以确定当前分支，以及当前分支的提交点。

主分支名为`master`，当我们创建新的分支，如`dev`，实际上创建的是名为`dev`的指针，指向`master`相同的提交，并且将`HEAD`也指向`dev`这个指针。

如果我们需要合并两个分支，我们只需要将`master`直接指向`dev`的当前提交即可。合并完之后，我们甚至可以删除`dev`分支，因为删除该分支仅仅是删除这个指针，此时我们又只剩下了一个`master`主分支。

### 创建切换分支

首先，我们创建`dev`分支，然后切换到`dev`分支：

```shell
$ git checkout -b dev
Switched to a new branch 'dev'
```

`git checkout`命令加上`-b`参数表示创建并切换，相当于以下两条命令：

```shell
$ git branch dev
$ git checkout dev
Switched to branch 'dev'
```

然后，用`git branch`命令查看当前分支：

```shell
$ git branch
* dev
  master
```

`git branch`命令会列出所有分支，当前分支前面会标一个`*`号。

之后在进行提交，都是在 dev 分支上进行提交。

### switch

我们注意到切换分支使用`git checkout <branch>`，而前面讲过的撤销修改则是`git checkout -- <file>`，同一个命令，有两种作用，确实有点令人迷惑。

实际上，切换分支这个动作，用`switch`更科学。因此，最新版本的 Git 提供了新的`git switch`命令来切换分支：

创建并切换到新的`dev`分支，可以使用：

```
$ git switch -c dev
```

直接切换到已有的`master`分支，可以使用：

```
$ git switch master
```

使用新的`git switch`命令，比`git checkout`要更容易理解。

### 合并分支

现在，我们把`dev`分支的工作成果合并到`master`分支上：

```shell
$ git merge dev
Updating d46f35e..b17d20e
Fast-forward
 readme.txt | 1 +
 1 file changed, 1 insertion(+)
```

`git merge`命令用于合并指定分支到当前分支。

上面的`Fast-forward`信息，Git 告诉我们，这次合并是“快进模式”，也就是直接把`master`指向`dev`的当前提交，所以合并速度非常快。

当然，也不是每次合并都能`Fast-forward`。

### 删除分支

合并完成后，就可以放心地删除`dev`分支了：

```shell
$ git branch -d dev
Deleted branch dev (was b17d20e).
```

删除后，查看`branch`，就只剩下`master`分支了：

```shell
$ git branch
* master
```

因为创建、合并和删除分支非常快，所以 Git 鼓励你使用分支完成某个任务，合并后再删掉分支，这和直接在`master`分支上工作效果是一样的，但过程更安全。

### 解决冲突

当 Git 无法自动合并分支时，就必须首先解决冲突。解决冲突后，再提交，合并完成。

解决冲突就是把 Git 合并失败的文件手动编辑为我们希望的内容，再提交。

Git 用<<<<<<<，=======，>>>>>>>标记出不同分支的内容

用`git log --graph`命令可以看到分支合并图。

### 分支管理策略

在实际开发中，我们应该按照几个基本原则进行分支管理：

首先，`master`分支应该是非常稳定的，也就是仅用来发布新版本，平时不能在上面干活；

那在哪干活呢？干活都在`dev`分支上，也就是说，`dev`分支是不稳定的，到某个时候，比如 1.0 版本发布时，再把`dev`分支合并到`master`上，在`master`分支发布 1.0 版本；

你和你的小伙伴们每个人都在`dev`分支上干活，每个人都有自己的分支，时不时地往`dev`分支上合并就可以了。

所以，团队合作的分支看起来就像这样：

![branch](./Pictures/branch.png)

通常，合并分支时，如果可能，Git 会用`Fast forward`模式，但这种模式下，删除分支后，会丢掉分支信息。

但是合并分支时，加上`--no-ff`参数就可以用普通模式合并，合并后的历史有分支，能看出来曾经做过合并。

```shell
$ git merge --no-ff -m "merge with no-ff" dev
 Merge made by the 'recursive' strategy.
 readme.txt | 1 +
 1 file changed, 1 insertion(+)
```

### Bug 分支

软件开发中，bug 就像家常便饭一样。有了 bug 就需要修复，在 Git 中，由于分支是如此的强大，所以，每个 bug 都可以通过一个新的临时分支来修复，修复后，合并分支，然后将临时分支删除。

1. 不进行提交，即可暂存现在的任务内容

并不是你不想提交，而是工作只进行到一半，还没法提交，预计完成还需 1 天时间。但是，必须在两个小时内修复该 bug，怎么办？

幸好，Git 还提供了一个 stash 功能，可以把当前工作现场“储藏”起来，等以后恢复现场后继续工作：

```shell
$ git stash
Saved working directory and index state WIP on dev: f52c633 add merge
```

现在，用 git status 查看工作区，就是干净的（除非有没有被 Git 管理的文件），因此可以放心地创建分支来修复 bug。

2. 创建 bug 分支，修复 bug

首先确定要在哪个分支上修复 bug，假定需要在 master 分支上修复，就从 master 创建临时分支：

```shell
$ git checkout master
Switched to branch 'master'
Your branch is ahead of 'origin/master' by 6 commits.
  (use "git push" to publish your local commits)

$ git checkout -b issue-101
Switched to a new branch 'issue-101'
```

3. 修复完成后，切换到 master 分支，并完成合并，最后删除 issue-101 分支：

```bash
$ git switch master
Switched to branch 'master'
Your branch is ahead of 'origin/master' by 6 commits.
  (use "git push" to publish your local commits)

$ git merge --no-ff -m "merged bug fix 101" issue-101
Merge made by the 'recursive' strategy.
 readme.txt | 2 +-
 1 file changed, 1 insertion(+), 1 deletion(-)
```

4. 切换回当前开发分支

用`git stash list`命令查看之前的工作现场

Git 把 stash 内容存在某个地方了，但是需要恢复一下，有两个办法：

- 用`git stash apply`恢复，但是恢复后，stash 内容并不删除，你需要用`git stash drop`来删除；

- 用`git stash pop`，恢复的同时把 stash 内容也删了：

你可以多次 stash，恢复的时候，先用 git stash list 查看，然后恢复指定的 stash，用命令：

```bash
$ git stash apply stash@{0}
```

5. 在 dev 分支上修复同样的 bug

同样的 bug，要在 dev 上修复，我们只需要把`4c805e2 fix bug 101`这个提交所做的修改“复制”到 dev 分支。注意：我们只想复制`4c805e2 fix bug 101`这个提交所做的修改，并不是把整个 master 分支 merge 过来。

为了方便操作，Git 专门提供了一个 cherry-pick 命令，让我们能复制一个特定的提交到当前分支：

```bash
$ git branch
* dev
  master
$ git cherry-pick 4c805e2
[master 1d4b803] fix bug 101
 1 file changed, 1 insertion(+), 1 deletion(-)
```

Git 自动给 dev 分支做了一次提交，注意这次提交的 commit 是`1d4b803`，它并不同于 master 的`4c805e2`，因为这两个 commit 只是改动相同，但确实是两个不同的 commit。用`git cherry-pick`，我们就不需要在 dev 分支上手动再把修 bug 的过程重复一遍。

既然可以在`master`分支上修复 bug 后，在`dev`分支上可以“重放”这个修复过程，那么也可以直接在`dev`分支上修复 bug，然后在`master`分支上“重放”。不过你仍然需要`git stash`命令保存现场，才能从`dev`分支切换到`master`分支。

### Feature 分支

添加一个新功能时，你肯定不希望因为一些实验性质的代码，把主分支搞乱了，所以，每添加一个新功能，最好新建一个 feature 分支，在上面开发，完成后，合并，最后，删除该 feature 分支。

如果要丢弃一个没有被合并过的分支，可以通过`git branch -D <name>`强行删除。

### 多人协作

一般远程仓库会有一个默认的主分支`main`或者`master`，多人开发时，一般还会有一个`dev`分支。

当你的小伙伴从远程库 clone 时，默认情况下，你的小伙伴只能看到本地的`master`分支。当他要在`dev`分支上开发，就必须创建远程`origin`的`dev`分支到本地，于是他用这个命令创建本地`dev`分支：

```bash
$ git checkout -b dev origin/dev
```

现在，他就可以在`dev`上继续修改，然后，时不时地把`dev`分支`push`到远程。

当他人在远程设置`dev`分支时，你自己本地也有一个`dev`分支，在将你自己的分支推送前，需要指定本地`dev`分支和远程`origin/dev`分支的链接：

```bash
$ git branch --set-upstream-to=origin/dev dev
Branch 'dev' set up to track remote branch 'dev' from 'origin'.
```

在多人对`dev`开发时，难免会遇到多人修改提交有冲突的时候，这个时候需要先用`git pull`把最新的提交从`origin/dev`抓下来，然后，在本地合并，解决冲突，再推送。

因此，多人协作的工作模式通常是这样：

1. 首先，可以试图用`git push origin <branch-name>`推送自己的修改；

2. 如果推送失败，则因为远程分支比你的本地更新，需要先用`git pull`试图合并；

3. 如果合并有冲突，则解决冲突，并在本地提交；

4. 没有冲突或者解决掉冲突后，再用`git push origin <branch-name>`推送就能成功！

如果`git pull`提示`no tracking information`，则说明本地分支和远程分支的链接关系没有创建，用命令`git branch --set-upstream-to <branch-name> origin/<branch-name>`。

### Rebase 变基

* rebase操作可以把本地未push的分叉提交历史整理成直线；
* rebase的目的是使得我们在查看历史提交的变化时更容易，因为分叉的提交需要三方对比。

举例而言，当前的git历史如下：

```bash
$ git log --graph --pretty=oneline --abbrev-commit
* 582d922 (HEAD -> master) add author
* 8875536 add comment
* d1be385 (origin/master) init hello
*   e5e69f1 Merge branch 'dev'
|\  
| *   57c53ab (origin/dev, dev) fix env conflict
| |\  
| | * 7a5e5dd add env
| * | 7bd91f1 add new env
···
```

注意到Git用`(HEAD -> master)`和`(origin/master)`标识出当前分支的`HEAD`和远程`origin`的位置分别是`582d922 add author`和`d1be385 init hello`，本地分支比远程分支快两个提交。

使用`git pull`拉取远程提交并合并之后，现在我们本地分支比远程分支超前3个提交。

```bash
$ git log --graph --pretty=oneline --abbrev-commit
*   e0ea545 (HEAD -> master) Merge branch 'master' of github.com:michaelliao/learngit
|\  
| * f005ed4 (origin/master) set exit=1
* | 582d922 add author
* | 8875536 add comment
|/  
* d1be385 init hello
...
```

这个时候，rebase就派上了用场。我们输入命令`git rebase`

```bash
$ git rebase
First, rewinding head to replay your work on top of it...
Applying: add comment
Using index info to reconstruct a base tree...
M	hello.py
Falling back to patching base and 3-way merge...
Auto-merging hello.py
Applying: add author
Using index info to reconstruct a base tree...
M	hello.py
Falling back to patching base and 3-way merge...
Auto-merging hello.py
```

再用`git log`看看

```bash
$ git log --graph --pretty=oneline --abbrev-commit
* 7e61ed4 (HEAD -> master) add author
* 3611cfe add comment
* f005ed4 (origin/master) set exit=1
* d1be385 init hello
...
```

Git把我们本地的提交`3611cfe add comment`和`7e61ed4 add author`“挪动”了位置，放到了`f005ed4 (origin/master) set exit=1`之后，这样，整个提交历史就成了一条直线。rebase操作前后，最终的提交内容是一致的，但是，我们本地的`commit`修改内容已经变化了，它们的修改不再基于`d1be385 init hello`，而是基于`f005ed4 (origin/master) set exit=1`，但最后的提交`7e61ed4`内容是一致的。

这就是rebase操作的特点：把分叉的提交历史“整理”成一条直线，看上去更直观。缺点是本地的分叉提交已经被修改过了

## 标签管理

tag就是一个让人容易记住的有意义的名字，它跟某个commit绑在一起。

发布一个版本时，我们通常先在版本库中打一个标签（tag），这样，就唯一确定了打标签时刻的版本。将来无论什么时候，取某个标签的版本，就是把那个打标签的时刻的历史版本取出来。所以，标签也是版本库的一个快照。

Git的标签虽然是版本库的快照，但其实它就是指向某个commit的指针（跟分支很像，但是分支可以移动，标签不能移动），所以，创建和删除标签都是瞬间完成的。 

### 创建标签

Git中标签的指令都很简单。

* `git tag <name>`就可以打一个新标签
* 用命令`git tag`查看所有标签，标签不是按时间顺序列出，而是按字母排序的
* 标签默认打在最新提交的commit上，但是也可以通过声明`commit id`来打标签，比如`git tag v0.9 f52c633`
* 可以用`git show <tagname>`查看标签信息
* 可以创建带有说明的标签，用`-a`指定标签名，`-m`指定说明文字：`git tag -a v0.1 -m "version 0.1 released" 1094adb`

注意：标签总是和某个commit挂钩。如果这个commit既出现在master分支，又出现在dev分支，那么在这两个分支上都可以看到这个标签

### 操作标签

* 如果标签打错了，也可以删除：`git tag -d <tagname>`
* 创建的标签都存储在本地，因此可以安全的删除，但是如果要将某个标签推送到远程，可以使用指令：`git push origin <tagname>`
* 或者可以一次性推送全部没有被推送的标签：`git push origin --tags`
* 如果要删除推送到远程的标签，需要首先删除本地标签，然后再使用指令：`git push origin :refs/tags/v0.9`
