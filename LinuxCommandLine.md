# The Linux Command Line

## 命令速查

```sh
cd ~/.ssh // 查看ssh秘钥
lsof -i tcp:port // 查看端口
kill -9 PID // 根据PID杀进程
bg // 查看正在运行的任务
fg [n] // 将后台任务调回到前台运行
tree -I 'node_modules|dist' // 生成目录树,I参数表忽略
pwd // 显示用户当前工作目录
cd // Change Directory
echo // shell下的打印命令
cat // 连接文件
touch // 创建文件
mkdir // 创建目录
cp file1 file2 // 复制文件
cp -r file1 file2 // 复制文件
rm file // 删除文件
rm -r dir // 删除目录
touch a.txt // 创建文件
ssh user@host // 连接远程主机
exit // 退出
logout // 登出
```

## Chap1. Intro

> "Graphical user interfaces make easy task easy, while command line interfaces make difficult tasks possible."

"自由"的真正含义在于其值得是一台没有任何秘密的计算机，可以从它哪里了解一切。

## Chap2. Shell

shell 是一个程序，接受从键盘输入的命令，把命令传递给操作系统去执行。`bash`是`Bourne Again SHell`的首字母缩写，`bash`是最初`Unix`上由`Steve Bourne`所写的`shell`程序`sh`的增强版。

### 终端仿真器(terminal emulator)

与 shell 交互的终端仿真器程序，一般为`terminal`

`[me@linuxbox ~]$`

这叫做 shell 提示符，当 shell 准备好接受输入时，它就会出现。通常是用户名@主机名，紧接着是当前工作目录。如果提示符最后一个字符是`#`，而不是`$`，那么这个终端就会有超级用户权限。

```bash
TeemoMac:~ mac$ date
2017年 9月 5日 星期二 10时20分02秒 CST
```

```bash
TeemoMac:~ mac$ cal
     九月 2017
日 一 二 三 四 五 六
                1  2
 3  4  5  6  7  8  9
10 11 12 13 14 15 16
17 18 19 20 21 22 23
24 25 26 27 28 29 30
```

- `df`查看磁盘剩余空间的数量
- `free`显示空闲内存的数量
- `exit`终止终端会话

## Chap3. 文件系统中跳转

- pwd - Print name of current working directory
- cd - Change directory
- ls - List directory contents

```bash
TeemoMac:~ mac$ pwd
/Users/mac
```

Home 目录是唯一允许用户对文件进行写入的地方，在 OSX 上使用`echo $HOME`显示 home 目录路径。

```bash
TeemoMac:~ mac$ echo $HOME
/Users/mac
```

符号`.`指的是工作目录，`..`指的是工作目录的父目录

在几乎所有的情况下，你可以省略 “./”。它是隐含的。

| 快捷键        | 运行结果                     |
| ------------- | ---------------------------- |
| cd            | 更改工作目录到 Home          |
| cd -          | 更改工作目录到先前的工作目录 |
| cd ~user_name | 更改工作目录到用户 Home 目录 |

### 关于文件名的重要规则

1. 以`.`字符开头的文件名是隐藏文件。`ls`命令不能列出它们，用`ls-a`命令就可以。
2. 文件名和命令名区分大小写敏感。`File1`和`file1`是两个不同的文件名。
3. Linux 没有文件扩展名的概念，可以以任何方式给文件起名。
4. 建议不要在文件名中使用空格。

## chap4. 探究操作系统

- ls - List directory contents
- file - Determine file type
- less - View file contents

  1.`ls`命令

`ls`可以指定目录

```bash
TeemoMac:~ mac$ ls Desktop/
Adobe Photoshop CC 2014 Steam
```

`~`代表 Home 目录，`ls`后可以指定多个目录，用空格隔开

```bash
TeemoMac:~ mac$ ls Desktop/ Desktop/GIT/
Desktop/:
Adobe Photoshop CC 2014 Steam

Desktop/GIT/:
BLOG full-stack script
```

`ls-l`结果以长模式输出

```bash
TeemoMac:~ mac$ ls -l Desktop/
total 1448
-rw-r--r--@  1 mac  staff     848  8 16 21:19 Adobe Photoshop CC 2014
-rw-r--r--@  1 mac  staff     748  8 16 21:19 Eclipse
drwxr-xr-x  21 mac  staff     714  8 24 21:01 GIT
```

2.选项和参数

大多数命令看起来是这样

`command -options arguments`

option 中有长选项和短选项，许多命令允许短选项串在一起使用，长选项由两个中划线加上一个字组成。

`TeemoMac:~ mac$ ls -lt`

| 选项 | 长选项           | 描述                                                    |
| ---- | ---------------- | ------------------------------------------------------- |
| -a   | --all            | 列出所有文件，包括隐藏文件                              |
| -d   | --directory      | 目录会像文件一样列出，通常和-l 一起查看目录详细信息     |
| -F   | --classify       | 每个所列出名字后面加上一个指示符，若果是目录，则加上`/` |
| -h   | --human-readable | 以长格式列出时，以可读形式显示文件大小                  |
| -l   |                  | 长格式显示结果                                          |
| -r   | --reverse        | 相反的顺序显示结果                                      |
| -S   |                  | 命令输出结果按照文件大小来排序                          |
| -t   |                  | 按照修改时间来排序                                      |

3.深入研究长格式输出

```bash
TeemoMac:Desktop mac$ ls -l
total 1448
-rw-r--r--@  1 mac  staff     848  8 16 21:19 Adobe Photoshop CC 2014
-rw-r--r--@  1 mac  staff     748  8 16 21:19 Eclipse
drwxr-xr-x  21 mac  staff     714  8 24 21:01 GIT
```

各输出字段的含义

| 字段                    | 含义                                                                                                                                                  |
| ----------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- |
| -rw-r--r--              | 对于文件的访问权限。第一个字符指明文件类型。开头的`-`说明是一个普通文件，`d`表明是一个目录。后面是文件所有者，文件所属组中成员,其他所有人的访问权限。 |
| 1                       | 文件硬链接数目                                                                                                                                        |
| mac                     | 文件拥有者的用户名                                                                                                                                    |
| staff                   | 文件所属用户组的名字                                                                                                                                  |
| 848                     | 以字节数表示的文件大小                                                                                                                                |
| 8 16 21:19              | 上次修改文件的时间和日期                                                                                                                              |
| Adobo Photoshop CC 2014 | 文件名                                                                                                                                                |

4.确定文件类型

Linux 中并不要求文件名来反映文件内容

`file filename`查看文件内容的简单描述

```bash
TeemoMac:Reading mac$ file ES6.md
ES6.md: UTF-8 Unicode text, with overstriking
```

> Everything is a file!

5.用`less`浏览文件内容

这样使用 less 命令

`less filename`

`TeemoMac:Reading mac$ less ES6.md`

| 命令        | 行为                                                     |
| ----------- | -------------------------------------------------------- |
| G           | 移动到最后一行                                           |
| g           | 移动到开头一行                                           |
| /characters | 向前查找指定的字符串                                     |
| n           | 向前查找下一个出现的字符串，这个字符串是之前所指定查找的 |
| h           | 显示帮助屏幕                                             |
| q           | 退出 less 程序                                           |

## 5. 操作文件和目录

- cp - Copy files and directories
- mv - Move/rename files and directories
- mkdir - Create directories
- rm - Remove files and directories
- ln - Create hard and symbolic links

对于复杂的文件操作任务，使用命令行程序比较容易完成。

### 通配符

| 通配符        | 意义                               |
| ------------- | ---------------------------------- |
| \*            | 匹配任意多个字符(包括零个或一个)   |
| ?             | 匹配任意一个字符(不包括零个)       |
| [characters]  | 匹配任意一个属于字符集中的字符     |
| [!characters] | 匹配任意一个不是字符集中的字符     |
| [[:class]]    | 匹配任意一个属于指定字符类中的字符 |

| 字符类    | 意义                   |
| --------- | ---------------------- |
| [:alnum:] | 匹配任意一个字母或数字 |
| [:alpha:] | 匹配任意一个字母       |
| [:digit:] | 匹配任意一个数字       |
| [:lower:] | 匹配任意一个小写字母   |
| [:upper]  | 匹配任意一个大写字母   |

| 模式                   | 匹配对象                                                  |
| ---------------------- | --------------------------------------------------------- |
| \*                     | 所有文件                                                  |
| g\*                    | 文件名以"g"开头的文件                                     |
| b\*.txt                | 以"b"开头，中间有零个或任意多个字符，并以".txt"结尾的文件 |
| Dara???                | 以"Data"开头，气候紧接着 3 个字符的文件                   |
| [abc]\*                | 文件名以"a","b"或"c"开头的文件                            |
| BACKUP.[0-9][0-9][0-9] | 以"BACKUP."开头，并紧接着 3 个数字的文件                  |
| [[:upper:]]\*          | 以大写字母开头的文件                                      |
| [![:digit:]]\*         | 不以数字开头的文件                                        |
| \*[[:lower:]123]       | 文件名以小写字母结尾，或以"1"，"2"，或"3"结尾的文件       |

### 链接

硬链接(hard link)和符号链接(symlink 也叫软链接)。

硬链接是指针，所有的硬链接都是指向同一个磁盘块。 删除一个指针不会真正删除文件，只有把所有的指针都删除才会真正删除文件。 软连接是另外一种类型的文件，保存的是它指向文件的全路径， 访问时会替换成绝对路径。

[5 分钟让你明白“软链接”和“硬链接”的区别](https://www.jianshu.com/p/dde6a01c4094)

## chap5. 操作文件和目录

- cp 复制文件和目录
- mv 移动/重命名文件和目录
- mkdir 创建目录
- rm 删除文件和目录
- ln 创建硬链接和符号链接

## chap7. 重定向(I/O 重定向)

- cat - Concatenate files
- sort - Sortlines of text
- uniq - Report or omit repeated lines
- grep - Print lines matching a pattern
- wc - Print newline, word, and byte counts for each file
- head - Output the first part of a file
- tail - Output the last part of a file
- tee - Read from standard input and write to standard ouput and files

### 重定向标准输出

I/O 重定向允许我们来重定义标准输出送到哪里，使用`>`重定向符

当我们使用`>`重定向符到文件的时候，目标文件总是从开头被重写

使用`>>`追加到文件内容后面

### cat - 连接文件

`cat`命令读取一个或多个文件，然后复制他们到标准输出

### 管道线`|`

一个命令的标准输出可以管道到另一个命令的标准输入，使用`|`(管道操作符)

### 过滤器 sort

过滤器接受输入，以某种方式改变它，然后输出它

```sh
ls /bin | sort | less
```

### uniq - 忽略重复行

```sh
ls /bin | sort | uniq | less
```

### grep - 打印匹配行

用来找到文件中的匹配文本

```sh
ls /bin | sort | uniq | grep zip
```

### head/tail - 打印文件开头/结尾部分

使用`-n`来调整打印行数

```sh
head -n 5 javascript.md
```

使用`-f`参数来检测文件，当有新的内容添加到文件中，会立即出现在屏幕上

```sh
tail -f /var/log
```
