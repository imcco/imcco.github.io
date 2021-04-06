---
title: Git查看、删除、重命名远程分支和tag
tags: Git
category: Git
abbrlink: 30226
date: 2018-01-04 21:34:36
---
![image](http://ovi3ob9p4.bkt.clouddn.com/TIETU/CT0069.jpg)

Git查看、删除、重命名远程分支和tag
<!--more-->

## 查看远程分支
加上-a参数可以查看远程分支，远程分支会用红色表示出来（如果你开了颜色支持的话）：

```shell
$ git branch -a
  master
  remote
  tungway
  v1.52
* zrong
  remotes/origin/master
  remotes/origin/tungway
  remotes/origin/v1.52
  remotes/origin/zrong
```

## 删除远程分支和tag
在Git v1.7.0 之后，可以使用这种语法删除远程分支：

```shell
$ git push origin --delete  <branchName>
```

删除tag这么用：

```shell
git push origin --delete tag  <tagName>

```

否则，可以使用这种语法，推送一个空分支到远程分支，其实就相当于删除远程分支：

```shell
git push origin : <branchName>
```

这是删除tag的方法，推送一个空tag到远程tag：

```shell
git tag -d <tagname>
git push origin :refs/tags/<tagname>

```

两种语法作用完全相同。
删除不存在对应远程分支的本地分支
假设这样一种情况：
1. 我创建了本地分支b1并pull到远程分支 origin/b1；
2. 其他人在本地使用fetch或pull创建了本地的b1分支；
3. 我删除了 origin/b1 远程分支；
4. 其他人再次执行fetch或者pull并不会删除这个他们本地的 b1 分支，运行 git branch -a 也不能看出这个branch被删除了，如何处理？

使用下面的代码查看b1的状态：

```shell
$ git remote show origin
* remote origin
  Fetch URL: git@github.com:xxx/xxx.git
  Push  URL: git@github.com:xxx/xxx.git
  HEAD branch: master
  Remote branches:
    master                 tracked
    refs/remotes/origin/b1 stale (use 'git remote prune' to remove)
  Local branch configured for 'git pull':
    master merges with remote master
  Local ref configured for 'git push':
    master pushes to master (up to date)
```

这时候能够看到b1是stale的，使用 git remote prune origin 可以将其从本地版本库中去除。
更简单的方法是使用这个命令，它在fetch之后删除掉没有与远程分支对应的本地分支：

```shell
git fetch -p
```

## 重命名远程分支
在git中重命名远程分支，其实就是先删除远程分支，然后重命名本地分支，再重新提交一个远程分支。
例如下面的例子中，我需要把 devel 分支重命名为 develop 分支：

```shell
$ git branch -av
* devel                             752bb84 Merge pull request #158 from Gwill/devel
  master                            53b27b8 Merge pull request #138 from tdlrobin/master
  zrong                             2ae98d8 modify CCFileUtils, export getFileData
  remotes/origin/HEAD               -> origin/master
  remotes/origin/add_build_script   d4a8c4f Merge branch 'master' into add_build_script
  remotes/origin/devel              752bb84 Merge pull request #158 from Gwill/devel
  remotes/origin/devel_qt51         62208f1 update .gitignore
  remotes/origin/master             53b27b8 Merge pull request #138 from tdlrobin/master
  remotes/origin/zrong              2ae98d8 modify CCFileUtils, export getFileData
```

删除远程分支：

```shell
$ git push --delete origin devel
To git@github.com:zrong/quick-cocos2d-x.git
 - [deleted]         devel
```

重命名本地分支：

```shell
git branch -m devel develop
```

推送本地分支：

```shell
$ git push origin develop
Counting objects: 92, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (48/48), done.
Writing objects: 100% (58/58), 1.38 MiB, done.
Total 58 (delta 34), reused 12 (delta 5)
To git@github.com:zrong/quick-cocos2d-x.git
 * [new branch]      develop -> develop
```

然而，在 github 上操作的时候，我在删除远程分支时碰到这个错误：

```shell
$ git push --delete origin devel
remote: error: refusing to delete the current branch: refs/heads/devel
To git@github.com:zrong/quick-cocos2d-x.git
 ! [remote rejected] devel (deletion of the current branch prohibited)
error: failed to push some refs to 'git@github.com:zrong/quick-cocos2d-x.git'

```

这是由于在 github 中，devel 是项目的默认分支。要解决此问题，这样操作：
进入 github 中该项目的 Settings 页面；
设置 Default Branch 为其他的分支（例如 master）；
重新执行删除远程分支命令。
把本地tag推送到远程

```shell
git push --tags
```

获取远程tag

```shell
git fetch origin tag
```
