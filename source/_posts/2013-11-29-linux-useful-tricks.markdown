---
layout: post
title: "Linux Useful Tricks"
date: 2013-11-29 08:18
comments: true
categories: [Linux]
---

## 课后任务

使用vagrant搭建Linux虚拟机

[官方网址](http://docs.vagrantup.com/v2/getting-started/index.html)

1 首先安装 [virtualbox](http://www.virtualbox.org/)

2 安装[vagrant](http://docs.vagrantup.com/v2/installation/)

3 打开cmd，或者终端，运行以下命令，注意这一步会下载一个Linux发行版，大约3，400M吧。所以，你也可以用迅雷到网上下载下来，然后把 `http://files.vagrantup.com/precise32.box` 改成你文件的路径，比如我的是`file:///Users/pieux/Documents/o/precise32.box`

```
$ vagrant init precise32 http://files.vagrantup.com/precise32.box
$ vagrant up
```

4 SSH 进入linux吧！`vagrant ssh`，退出就是 `exit`

5 关闭Linux的命令是，休眠`vagrant suspend`，或者关闭`vagrant halt`。


## man 命令

```
1	使用者在shell中可以操作的指令或可执行档
2	系統核心可呼叫的函数与工具等
3	一些常用的函数(function)与函数库(library)，大部分是C的函数库(libc)
4	装置档案的说明，通常在/dev下的档案
5	设定档或者是某些档案的格式
6	游戏(games)
7	惯例与协定等，例如Linux档案系统、网络协定、ASCII code等等的說明
8	系統管理員可用的管理指令
9	跟kernel有关的文件
```

## tree 命令

```
tree
-a
-A
-C
man tree
```

# shell 快捷键

编辑命令

```
Ctrl + a ：移到命令行首
Ctrl + e ：移到命令行尾
Ctrl + f ：按字符前移（右向）
Ctrl + b ：按字符后移（左向）
Alt + f ：按单词前移（右向）
Alt + b ：按单词后移（左向）
Ctrl + u ：从光标处删除至命令行首
Ctrl + k ：从光标处删除至命令行尾
Ctrl + d ：删除光标处的字符
Ctrl + y ：粘贴至光标后
```

重新执行命令

```
Ctrl + r：逆向搜索命令历史
Ctrl + g：从历史搜索模式退出
Ctrl + p：历史中的上一条命令
Ctrl + n：历史中的下一条命令
```

控制命令

```
Ctrl + l：清屏
Ctrl + s：阻止屏幕输出
Ctrl + q：允许屏幕输出
Ctrl + c：终止命令
Ctrl + z：挂起命令
```

## grep 命令

```
grep
-v 反选
-E 正则
-i 忽略大小写
-n 加上行号
```
简单的正则表达式：

```
^ 行首
$ 行尾
. 任意字符
* >0个重复
+ >1个重复
[Gg], [A-Za-z]
\< 单词首
\> 单词尾
```

## 进程相关

```
ps -aux | grep process-name
pgrep
kill
pkill
killall
```

## find

```
find -name
find -not -name
-i
-type fdl
-mindepth 3
-maxdepth 3
-exec ls -l {} \;
| xargs ls -l
```


## 其他

```
touch 创建文件
alias 别名
history
sudo !! 重执行上一条命令
cd - 进入之前的目录
wc
chmod 
du -hd 1
du -h --max-depth=1
Ctrl-Z 挂起
fg 恢复
> .gitignore
< .gitignore
mv filename.{old,new}
rm !(*.foo|*.bar|*.baz)
```

## git 相关

```
git add -A
git reset --soft
git reset --mixed
git reset --hard
```



## 学习资料推荐

http://www.commandlinefu.com/commands/tagged/800/commandfu
