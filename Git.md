# Git

## 编号含义

- A：add，新增
- C：conflict，冲突
- D：delete，删除
- M：modify，本地已经修改
- G：modify and merGed，本地文件修改并且和服务器的进行合并
- U：update，从服务器更新
- R：replace，从服务器替换
- I：ignored，忽略

## 常用命令

```bash
git reset HEAD . // 撤销git add .操作
git reset HEAD~[n] // 回退到n个提交之前
git reset [version] // 回退到某个版本
git fetch origin xxx // 拉取远程xxx分支
git checkout -b xxx origin/xxx // 切换并创建xxx分支，可以从后面的基础上切出
git branch -a // 查看所有分支
git branch -d dev // 删除dev分支
git rm --cached // 删除文件的git追踪，但是本地还会有
git log --stat/-p // 查看文件修改记录
git reflog // 查看操作记录
git stash // 工作区暂存
git stash list // 工作区暂存列表
git stash apply // 恢复工作区
git stash drop // 删除工作区
git reset --hard 版本号 // 强制回退某个版本
cd ~/.ssh //查看ssh秘钥
git config --global user.name "John Doe"
git config --global user.email johndoe@example.com
git config --local user.name "John Doe"
git config --local user.email johndoe@example.com // 只修改项目配置name
git remote add origin git@github.com:Xuemuyang/ts-axios.git // 与远程仓库关联
git branch --merged | egrep -v "(^\*|master|dev)" | xargs git branch -d // 删除已经合并的分支
git checkout . // 清空改动
git clean -df // 清空新建的文件夹和文件
```

oh-my-zsh 中对 git 命令进行了简化

```zsh
glg = git log
ga = git add
gaa = git add all
gb = git branch
gco = git checkout
gl = git pull
gp = git push
g reflog = git reflog
glol 查看log记录
ggf = git push --force origin $(current_branch)
gpsup = git push --set-upstream origin $(current_branch)
ggpull = git pull origin $(git_current_branch)
ggpush = git push origin $(git_current_branch)
```

| option        | description                    |
| ------------- | ------------------------------ |
| -p            | 按补丁格式显示每个更新之间差异 |
| --stat        | 显示每次更新的文件修改统计信息 |
| --name-status | 显示新增，修改，删除的文件清单 |

## 分支功能

| type     | desc                                                            |
| -------- | --------------------------------------------------------------- |
| feat     | A new feature                                                   |
| fix      | A bug fix                                                       |
| docs     | Documentation only changes                                      |
| style    | Changes that do not affect the meaning of the code(white-space) |
| refactor | A code change that neither fixes a bug nor adds a feature       |
| perf     | A code change that improves performance                         |
| test     | Adding missing tests or correcting existing tests               |
| build    | Changes that affec the build system or external dependencies    |
| ci       |
| chore    | Other changes that don't modify src or test files               |

## ssh 相关

```sh
ssh-keygen
ssh-add -K [path/to/your/ssh-key] // 将秘钥添加keychain中，添加私钥
```

## 添加默认忽略文件

编辑`~/.subversion/config`，找到`global-ignores`这一行，添加一下经常需要忽略的文件及目录

```git
global-ignores = *.o *.lo *.la *.al .libs *.so *.so.[0-9]* *.a *.pyc *.pyo node_modules .git .gitignore *.cache .DS_Store *.map .sass-cache *.swp .build
```

## 解决痛点

1. 历史修改追溯
1. 团队协作

## 创建版本库

把目录变成 Git 可管理的仓库

```sh
$git init
```

将文件添加到仓库

```sh
$git add readme.md
```

将文件提交到仓库，`-m` 后面是提交备注

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

在 Git 中，用 `HEAD` 表示当前版本(当前分支的指针)，上一个版本就是 `HEAD^`。

`git reset`回滚

```sh
$git reset --hard HEAD^
```

`git reflog`查看命令历史，这里可以查到版本 id

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

`origin`是 Git 默认的远程仓库的叫法。

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

将远程更新拉取到本地使用`git fetch`命令。

`git pull`命令取回远程某个分支的更新，再与本地的指定分支合并。

## 分支管理

```sh
$git branch -a
```

`-a`参数表示显示本地和远程的所有分支

```sh
$git checkout -b dev
```

`-b`参数表示创建并切换。

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

通常情况下 Git 会用`Fast forward`模式，删除分支会丢掉分支信息，可以强制禁用`Fast forward`模式。原理是在`merge`的时候`commit`一次，这样分支历史上就可以看出分支信息。

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

使用`git stash apply`恢复，之后 stash 内容并不删除，需要`git stash drop`来删除，另一种方式是`git stash pop`，恢复的同时把 stash 内容也删了。

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

## GitBook

### 分支

Git 的分支，其实本质上仅仅是指向提交对象的可变指针。

Git 的 “master” 分支并不是一个特殊分支。它就跟其它分支完全没有区别。之所以几乎每一个仓库都有`master`分支，是因为`git init`命令默认创建它，并且大多数人都懒得去改动它。

HEAD 是一个指针，指向当前所在的本地分支。

合并两个分支时，如果顺着一个分支走下去能够到达另一个分支，那么 Git 在合并两者的时候，只会简单的将指针向前推进（指针右移），因为这种情况下的合并操作没有需要解决的分歧——这就叫做 “快进（fast-forward）”，图解见下。

![fast-forword](./images/git/fast-forword1.png)

![fast-forword](./images/git/fast-forword2.png)

![merge](./images/git/merge1.png)

![merge](./images/git/merge2.png)

任何因包含合并冲突而有待解决的文件，都会以未合并状态标识出来。Git 会在有冲突的文件中加入标准的冲突解决标记。

### 2.4 撤消操作

```sh
$git commit --amend
```

提交后发现忘记了暂存某些需要的修改，可以像下面这样操作：

```sh
$git commit -m 'initial commit'
$git add forgotten_file
$git commit --amend
```

最终你只会有一个提交 - 第二次提交将代替第一次提交的结果。

## 选择修订版本

 在引用的末尾加上 `^`，Git 会将其解析为该引用的上一个提交。

可以使用 `HEAD^` 来查看上一个提交，也就是 “HEAD 的父提交”。

另一种方式是 `~`，在 `~` 后面加数字。

`HEAD~` 和 `HEAD^` 是等价的。

## git reset

三棵树

- HEAD 是当前分支引用的指针，它总是指向该分支上的最后一次提交。
- Index 是预计的下一次提交，即“暂存区”
- Working Directory

![git-reset](./images/git/reset-tree.png)

soft 是默认选项

![git-reset--soft](./images/git/reset-soft.png)

![git-reset--mixed](./images/git/reset-mixed.png)

![git-reset--hard](./images/git/reset-hard.png)

## 知识点整理

### git push

`git push origin master` // 将本地的master分支推送到origin主机的master分支。如果master不存在，则会被新建。
`git push origin HEAD:<name-of-remote-branch>` // push 到远程的指定分支上
