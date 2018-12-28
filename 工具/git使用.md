---
title: git使用
date: 2016-12-07 13:44:22
categories: 
- 工具
tags: 
- 工具
---

## 命令

#### git config
> git config --global credential.helper store  储存密码，只需要输入一次密码，以后都不需要输入，免输入密码的另一种方式

#### git add
> git add + 文件／路径 添加文件到本地暂存区

#### git branch
> git branch -d + 分支名 删除分支
> git branch + 分支名 创建分支

#### git remote
> git remote add origin git@server-name:path/repo-name.git     关联本地版本库到远程版本库

#### git push
> git push -u origin 远程分支名称   关联后第一次使用,推送master分支的所有内容到远程仓库
> git push origin 远程分支名称       推送最新修改到远程仓库

#### git fetch 获取远程版本库的提交
> git fetch origin 远程分支名       从远程获取最新版本到本地，不会自动merge

#### git pull
> git pull origin 远程分支名        从远程获取最新版本并merge到本地

#### git checkout
> git checkout + 分支名 切换到指定分支
> git checkout -b + 分支名 创建并切换到指定分支

> git checkout readme.txt 恢复文件到没有被修改的状态（使用版本库中文件替换工作区文件，因此即使文件被删除依然可用）
> git checkout . 恢复当前文件夹所有文件到没有被修改的状态

#### git diff
> git diff -- readme.txt             查看工作区与暂存区的差别  
> git diff HEAD -- readme.txt        查看工作区与master分支的差别  
> git diff --cached -- readme.txt    查看暂存区与master分支的差别 

#### git clone
> git clone + 仓库地址 克隆远程仓库代码到本地
> git clone -b + 分支名 + 仓库地址 克隆远程仓库代码指定分支到本地


#### git commit
> git commit -m 提交标签   提交暂存区到本地版本库，此时暂存区清空

#### git reset
> git reset HEAD readme.txt   把暂存区的修改回退到工作区
> git reset --hard HEAD^      回退到上一个版本，并把工作区文件更新
> git reset --hard commit_id  回退到指定版本，并把工作区文件更新，可配合git reflog使用

#### git revert
- git revert 撤销 某次操作，此次操作之前和之后的commit和history都会保留，并且把这次撤销 git revert是提交一个新的版本，将需要revert的版本的内容再反向修改回去，
版本会递增，不影响之前提交的内容
作为一次最新的提交
> git revert HEAD                  撤销前一次 commit
> git revert HEAD^               撤销前前一次 commit
> git revert commit （比如：fa042ce57ebbe5bb9c8db709f719cec2c58ee7ff）撤销指定的版本，撤销也会作为一次提交进行保存。适合已经提交到远程的回退。

#### git merge
> git merge --no-ff -m "合并标签" 需合并的分支   禁用Fast forward方式合并，此种方式合并可在分支历史看到合并分支的信息      
> git merge -m "合并标签" 需合并的分支   如果可能，会使用Fast forward方式合并，此种方式在删除feature分支后丢失分支信息，但可以保持分支干净

#### git rebase 分支变基

> git rebase -i [startpoint] [endpoint] 将多次提交合并修改，注意，不能修改任何已经提交到远程的commit。
> git rebase origin 让分支历史变为线性
> git rebase branch_name 合并分支，遇到冲突会需要一个一个解决，并且合并后的分支线为线性，像是没有发生过合并一样。
> git rebase --continue 解决冲突后继续合并
> git rebase --abort 取消本次合并，回到rebase前的状态

#### git help
> git help + 命令名 查看命令详细帮助

#### git status
> 查看状态

#### git log
> git log  查看当前分支提交日志

#### git reflog
> git reflog  查看命令历史记录

#### git tag
> git tag  标签名    打标签，默认标签打在最新提交的commit上。
> git tag 标签名 提交id   打标签到指定的提交上
> git tag -d 标签名      删除本地标签
> git push origin --tags  推送所有未提交的标签到远程分支

#### git gui
> git gui 基于Tcl/Tk的图形化工具，侧重提交等操作

#### git remote
> git remote update origin --prune  更新远程分支列表

#### git flow
> 使用标准git flow项目模型，简化git操作，标准项目规范

##### git flow命令详情

```
$ git flow help
usage: git flow <subcommand>

Available subcommands are:
   init      Initialize a new git repo with support for the branching model.
   feature   Manage your feature branches.
   bugfix    Manage your bugfix branches.
   release   Manage your release branches.
   hotfix    Manage your hotfix branches.
   support   Manage your support branches.
   version   Shows version information.
   config    Manage your git-flow configuration.
   log       Show log deviating from base branch.

Try 'git flow <subcommand> help' for details.

```

## git区

#### 工作区

- 在当前仓库中，新增，更改，删除文件这些动作，都发生在工作区里面

#### 暂存区

- 如果当前仓库，有文件更新，并且使用git add 命令，那么这些更新就会出现在暂存区中

#### 版本库 （）

- 当执行提交操作git commit时，暂存区的目录树写到版本库

## git版本

#### HEAD

- 在Git中，用HEAD表示当前版本，上一个版本就是HEAD^，上上一个版本就是HEAD^^，往上100个版本写100个^比较容易数不过来，所以写成HEAD~100

#### FETCH_HEAD

- 某个branch在服务器上的最新状态

#### 

## 分支模型

#### 常用分支

- 主分支 master
- 开发分支 develop
- 特性分支 feature
- 发布分支 release
- 热更新分支 hotfix
- 问题修复分支 issue

#### 各分支使用
- master分支是主分支，因此要时刻与远程同步
- dev分支是开发分支，团队所有成员都需要在上面工作，所以也需要与远程同步
- bug分支只用于在本地修复bug，就没必要推到远程了
- feature分支是否推到远程，取决于你是否和你的小伙伴合作在上面开发

#### 工作流

- 集中式工作流
只有一个master分支，开发者将代码克隆到本地，之后所有修改提交都在本地进行，直到某个时间点将本地代码合并到远程master分支。
- 功能开发工作流
关注功能开发，不直接往master提交代码保证它是稳定干净的，而是从master拉取feature分支进行功能开发，团队成员根据分工不同进行不同feature的开发，当功能开发完成后，会向master分支发起pull request，只有通过审核的代码才允许合并入master，也就是Code Review。
- Gitflow工作流
适合管理大型项目的发布和维护，整个开发周期master和develop分支一直存在，特性开发在feature进行，发布在release进行，bug修复在hotfix进行。
- Forking工作流
开源项目常用，有一个公开的中央仓库，其他开发者可以克隆这个仓库作为自己的私有仓库，只有开源项目维护者才可以向中央仓库push代码和接受代码贡献者向中央仓库发起的pull request请求。

## 最佳实践

#### 使用场景

* 在远程仓库新建一个项目，并克隆到本地
``` shell
git clone git@172.16.8.158:gao.xy/xyWeb.git;  
cd xyWeb;
touch README.md;
git add README.md;
git commit -m add README;
git push -u origin master; 
```

* 将本地项目已有项目上传到远程仓库
```
cd existing_folder;
git init;
git remote add origin + git仓库地址;
git add .;
git commit -m “init";
git push -u origin master;
```

* 新建开发分支进行开发
```
git branch dev;
git checkout dev;
```

* 合并开发分支到主分支
```
git chekout master;
git merge dev;
```

* 回退到文件未改动的状态
```
git checkout .
```

* 从远程获取master分支最新版本到本地
方式一：
```
git fetch origin master;
git log -p master..origin/master;
git merge origin/master;
```
方式二：
```
git pull origin master;
```


* 合并提交commit历史
```
git rebase -i 提交ID;

# 编辑提交历史 合并提交 
# 不能修改第一个提交 会报错类似Cannot 'squash' without a previous commit

git push;

```

* 合并之前的多个commit提交
```
# 回退版本库和暂存区内容，但工作区仍然是最新的内容，千万不要加--hard，否则工作区的代码也会回退，代码会丢失
git reset a4959da;
# 重新发起一次提交即可
git add .;
git commit -m "message";
```

* 强制推送本地分支到远程

```
git push origin 远程分支名 -f
```

* 强制远程分支覆盖本地
```
git fetch --all;
git reset --hard origin/master;
git pull;
```


* 回退到之前提交的一个版本
```
git reset --hard 提交ID
```

* 使提交历史在一条线上
```
# 在feature分支开发时，经常使用git rebase dev保持与dev同步代码，等feature开发完成后，切到dev分支，使用git merge feature，这时候merge是Fast-Forward，不会产生任何无用的commit。
git rebase
```

* 提交dev后后悔，想修改线上记录
```
git checkout dev;
git pull;
git checkout feature;
git rebase -i $commitId;
# 利用rebase修改到自己想要的记录后
git checkout dev;
git rebase feature;
git push;
```

#### git高级合并

- git merge --abort 退出本次合并，并返回合并前状态
- git merge -Xignore-space-change whitespace 忽略所有空白

##### git merge 策略

常规合并里分为三种:
解决（Resolve） 递归（Recursive） 章鱼（Octopus）
非常规两种:
我们的（Ours） 子树（Subtree）

##### git合并参数
在看策略之前，我们先看看关于merge在做处理时候的参数 
主要分为三种：

--ff --ff--only 快速合并 只快速合并（如果有冲突就失败） --no-ff 非快速合并 --squash 将合并过来的分支的所有不同的提交，当做一次提交，提交过来 --ff和--no-ff的区别是，当解决完冲突后，no-ff会生成一次commit eg: Merge branch 'suit' into master

## git发布环境

### 测试环境
    提供测试人员使用，代码分支除了可以使用master分支外，其他的分支也是可以的。


### 回归环境
    如果同时有好几个人参与同一个项目，那么基于master分支可能拉出非常多的开发分支，那么当这些分支合并到master上后，master上的功能可能受到影响，这种情况下，会使用一个回归环境，部署master分支的代码。


### 预发布环境
    这个环境中，一般会连接生产环境的数据库，使用生产环境的数据来进行测试。


### 灰度发布版本
    预发布环境过后，就是灰度发布了。由于一个项目，一般会部署到多台机器，所以灰度1台至三台，看看新功能是否ok，如果失败则只需要回滚几台，比价方便。注意，由于是灰度发布几种几台，所以一般会使用跳板机，然后进行域名绑定，这样才可以保证只访问有最新代码的服务器。

## 博客
- [廖雪峰博客](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/0013743858312764dca7ad6d0754f76aa562e3789478044000)
- [官方文档](https://git-scm.com/book/zh/v2)
- [git-merge完全解析](https://www.jianshu.com/p/58a166f24c81)
- [Git 最佳实践：分支管理](http://blog.jobbole.com/109466/?spm=a2c4e.11153940.blogcont612492.9.77183d2b99Bwaf)