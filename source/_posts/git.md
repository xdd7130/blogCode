---
---
title: git
date: 2019-07-04 22:24:57
categories:
- git
tags: [git]
---

## git
>初始化一个Git仓库，使用git init命令。

>添加文件到Git仓库，分两步：

```
使用命令git add <file>，注意，可反复多次使用，添加多个文件；
使用命令git commit -m <message>，完成。
```

### 工作区和暂存区

>首先需要理解工作区和版本库的概念：

工作区（Working Directory）：就是可以在电脑上看到的目录，比如目录文件夹，就是一个工作区；
版本库（Repository）: 工作区有一个隐藏目录.git，这个不算工作区，而是Git的版本库。
git的版本库中存放很多的东西，其中最重要的就是stage(index)的暂存区，还有git为我们创建的第一个分支master，以及指向maste分支的一个HEAD指针。
![01.png](01.png)

>将文件提交到版本库中需要两个步骤(也可以说是三个步骤)：

```bash
git status  可以查看哪些文件在本次修改了，以便我们清楚在下一步应该提交哪些文件
git add <filePath>   此命令是将file文件提交到暂存区，此命令是单个文件提交，也可以用
git add . 将工作区所有文件都提交，但一般没有这个必要，只需要将本次修改提交就好；
git commit -m'提交描述'    次命令是将暂存区的所有文件提交到版本库中的当前分支上，一般提交在后面加上本次修改的描述。
```

### 版本回退

总结：
>HEAD指向的版本就是当前版本，因此，Git允许我们在版本的历史之间穿梭，使用命令git reset --hard commit_id。
>穿梭前，用git log可以查看提交历史，以便确定要回退到哪个版本。
>要重返未来，用git reflog查看命令历史，以便确定要回到未来的哪个版本。

```bash
1、git log命令显示从最近到最远的提交日志
2、如果嫌输出信息太多，看得眼花缭乱的，可以试试加上--pretty=oneline参数：$ git log --pretty=oneline
看到的一大串类似1094adb...的是commit id（版本号）
3、要把当前版本回退到上一个版本，就可以使用git reset命令：$ git reset --hard HEAD^
```
### 撤销修改

```
$ git checkout -- readme.txt
```
一种是readme.txt自修改后还没有被放到暂存区，现在，撤销修改就回到和版本库一模一样的状态；
一种是readme.txt已经添加到暂存区后，又作了修改，现在，撤销修改就回到添加到暂存区后的状态。
总之，就是让这个文件回到最近一次git commit或git add时的状态。

场景1：当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，用命令git checkout -- file。
场景2：当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，第一步用命令git reset HEAD <file>，就回到了场景1，第二步按场景1操作。
场景3：已经提交了不合适的修改到版本库时，想要撤销本次提交，参考版本回退一节，不过前提是没有推送到远程库。

### 删除文件
命令git rm用于删除一个文件。如果一个文件已经被提交到版本库，那么你永远不用担心误删，但是要小心，你只能恢复文件到最新版本，你会丢失最近一次提交后你修改的内容。


## 远程仓库
### 添加远程仓库
1、要关联一个远程库，使用命令git remote add origin git@server-name:path/repo-name.git；

2、关联后，使用命令git push -u origin master第一次推送master分支的所有内容；

3、此后，每次本地提交后，只要有必要，就可以使用命令git push origin master推送最新修改；

分布式版本系统的最大好处之一是在本地工作完全不需要考虑远程库的存在，也就是有没有联网都可以正常工作，而SVN在没有联网的时候是拒绝干活的！当有网络的时候，再把本地提交推送一下就完成了同步，方便！

### 从远程库克隆
若我们从零开发，那么最好的方式是先创建远程库，然后，从远程库克隆。
1、首先，登陆GitHub，创建一个新的仓库。
2、勾选Initialize this repository with a README，这样GitHub会自动为我们创建一个README.md文件。创建完毕后，可以看到README.md文件。
3、用命令git clone克隆一个本地库。
## 分支管理
### 创建与合并分支
总结：
```
查看分支：git branch

创建分支：git branch <name>

切换分支：git checkout <name>

创建+切换分支：git checkout -b <name>

合并某分支到当前分支：git merge <name>

删除分支：git branch -d <name>
```
1、创建dev分支，然后切换到dev分支：$ git checkout -b dev
git checkout命令加上-b参数表示创建并切换，相当于以下两条命令：
$ git branch dev
$ git checkout dev

2、用git branch命令查看当前分支，git branch命令会列出所有分支，当前分支前面会标一个*号：$ git branch
3、我们就可以在dev分支上正常提交
4、dev分支的工作完成，我们就可以切换回master分支：git checkout master
5、我们把dev分支的工作成果合并到master分支上：git merge dev
git merge命令用于合并指定分支到当前分支
6、合并完成后，就可以放心地删除dev分支了：git branch -d dev
7、删除后，查看branch，就只剩下master分支了

### 解决冲突
当Git无法自动合并分支时，就必须首先解决冲突。解决冲突后，再提交，合并完成。

解决冲突就是把Git合并失败的文件手动编辑为我们希望的内容，再提交。

用git log --graph命令可以看到分支合并图。
### 分支管理策略
>在实际开发中，我们应该按照几个基本原则进行分支管理：

首先，master分支应该是非常稳定的，也就是仅用来发布新版本，平时不能在上面干活；

那在哪干活呢？干活都在dev分支上，也就是说，dev分支是不稳定的，到某个时候，比如1.0版本发布时，再把dev分支合并到master上，在master分支发布1.0版本；

你和你的小伙伴们每个人都在dev分支上干活，每个人都有自己的分支，时不时地往dev分支上合并就可以了。
### Bug分支
修复bug时，我们会通过创建新的bug分支进行修复，然后合并，最后删除；
当手头工作没有完成时，先把工作现场git stash一下，然后去修复bug，修复后，再git stash pop，回到工作现场。
### Feature分支
开发一个新feature，最好新建一个分支；

如果要丢弃一个没有被合并过的分支，可以通过git branch -D 【name】强行删除。
### 多人协作
多人协作的工作模式通常是这样：

1、首先，可以试图用git push origin 【branch-name】推送自己的修改；

2、如果推送失败，则因为远程分支比你的本地更新，需要先用git pull试图合并；

3、如果合并有冲突，则解决冲突，并在本地提交；

4、没有冲突或者解决掉冲突后，再用git push origin 【branch-name】推送就能成功！

如果git pull提示no tracking information，则说明本地分支和远程分支的链接关系没有创建，用命令git branch --set-upstream-to 【branch-name】 origin/【branch-name】。

这就是多人协作的工作模式，一旦熟悉了，就非常简单。

### Rebase
rebase操作可以把本地未push的分叉提交历史整理成直线；

rebase的目的是使得我们在查看历史提交的变化时更容易，因为分叉的提交需要三方对比。
## 标签管理
### 创建标签
命令git tag 【tagname】用于新建一个标签，默认为HEAD，也可以指定一个commit id；

命令git tag -a 【tagname】 -m "blablabla..."可以指定标签信息；

命令git tag可以查看所有标签
### 操作标签
命令git push origin 【tagname】可以推送一个本地标签；

命令git push origin --tags可以推送全部未推送过的本地标签；

命令git tag -d 【tagname】可以删除一个本地标签；

命令git push origin :refs/tags/【tagname】可以删除一个远程标签。

## git命令汇总
[Git Cheat Sheet](https://gitee.com/liaoxuefeng/learn-java/raw/master/teach/git-cheatsheet.pdf)