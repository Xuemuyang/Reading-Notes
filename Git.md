# Git

## 解决痛点

1. 历史修改追溯
1. 团队协作

## 创建版本库

把目录变成Git可管理的仓库

```sh
$git init
```

将文件添加到仓库

```sh
$git add readme.md
```

将文件提交到仓库，`-m`后面是提交备注

```sh
$git commit -m 'wrote a readme file'
```

## 版本控制

查看仓库当前的状态

```sh
$git status
```

查看修改了哪些

```sh
$git diff
```

修改文件提交也是一样的两个步骤，`git add`和`git commit`

如果不小心给文件改乱了，可以从最近的一个`commit`恢复。

`git log`可以查看最近提交的日志,`git log --graph`可以看到分支合并图。

```sh
$git log
```

在Git中，用`HEAD`表示当前版本，上一个版本就是`HEAD^`。

`git reset`回滚

```sh
$git reset --hard HEAD^
```

`git reflog`查看命令历史，这里可以查到版本id

```sh
$git reflog
```

## 工作区和暂存区

`git add`就是把文件修改添加到暂存区，`git commit`就是把暂存区的所有内容提交到当前分支。

如果每次修改不`add`到暂存区，那就不会加入到`commit`中。

`git checkout`就是让这个文件回到最近一次`git commit`或`git add`时的状态。可以让工作区回到版本库中的版本。

```sh
$git checkout -- readme.md
```

`git rm`删除版本库中文件

```sh
$git rm readme.md
```

## 远程仓库

```sh
$git remote add origin xxx
```

`origin`是Git默认的远程仓库的叫法。

第一次推送`master`分支需要加入`-u`参数，以后的推送或者拉取就可以简化命令。

```sh
$git push -u origin master
```

只要本地做了提交，就可以通过命令

```sh
$git push origin master
```

如果从零开始一个项目，最好的方式就是先创建远程库，然后`clone`。

```sh
$git clone xxx
```

## 分支管理

`-b`参数表示创建并切换。

```sh
$git checkout -b dev
```

相当于下面两条命令，`git branch xxx`是创建`xxx`分支，`git checkout xxx`是切换分支。

```sh
$git branch dev
$git checkout dev
```

`git merge`用于合并指定分支到当前分支。

```sh
$git merge dev
```

删除分支

```sh
$git branch -d dev
```

通常情况下Git会用`Fast forward`模式，删除分支会丢掉分支信息，可以强制禁用`Fast forward`模式。原理是在`merge`的时候`commit`一次，这样分支历史上就可以看出分支信息。

```sh
$git merge --no-ff -m "xxx" dev
```

`stash`功能可以把工作现场保存起来。

```sh
$git stash
```

查看保存的工作现场。

```sh
$git stash list
```

使用`git stash apply`恢复，之后stash内容并不删除，需要`git stash drop`来删除，另一种方式是`git stash pop`，恢复的同时把stash内容也删了。

使用`git branch -D xxx`来删除一个还没有被合并的分支。

使用`git remote`查看远程库的信息。`-v`显示更详细的信息(包含克隆地址)。

```sh
$git remote
```

多人协作的模式通常是这样的:

1. 首先通过`git push origin branch-name`推送自己修改
1. 推送失败，因为远程分支比本地新，需要`git pull`合并
1. 有冲突则解决冲突，并本地提交
1. 之后`git push origin branch-name`推送就能成功

如果`git pull`提示"no tracking information"，说明本地分支和远程分支的链接关系没有创建，用命令`git branch --set-upstream branch-name origin/branch-name`。