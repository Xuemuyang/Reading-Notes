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

`git log`可以查看最近提交的日志

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