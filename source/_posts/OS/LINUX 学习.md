---
title: LINUX 学习
date: 
categories:
- 运维
tags:
- linux
- CentOS
- 待续
---

### 一些命令示例
`ip addr` 改

`service network restart`

`yum update`



grep 过滤，-v 不包含，反转一下

```bash
cat anaconda-ks.cfg | grep "net" | grep -v "off"
```



## 帐号管理

### 添加新的用户账号

user add 

* -c 注释性描述
* -d 指定主目录，一般不指定
* -g 用户组
* -G 用户组的附属组
* -s 用户登录的shell 文件
* -u 用户号

```bash
groupadd ljs
useradd -s /bin/sh -g ljw -G adm,root ljw
```

### 删除账户

```bash
userdel -r ljw

userdel ljw
rm -rf /home/ljw
```

### 修改账户

usermod，加上-c, -d, -m, -g, -G, -s, -u, -o

### 用户密码管理

passwd

* -l 锁定口令，禁用账号
* -u 口令解锁
* -d 使账号无口令
* -f 强迫用户下次登录时修改口令

## 用户组管理

### 添加用户组

groupadd

* -g GID 指定新用户组的 GID（一定是数字）
* -o

### 删除用户组

groupdel

### 修改用户组

groupmod

* -n 新组名 + 旧组名
* -g
* -o

newgrp

su user1 切换用户

exit 退出当前所有用户



### linux 用户权限相关

/etc/passwd

/etc/shadow

/etc/group

修改 /etc/sudoers

```bash
user1 	ALL=(ALL)		NOPASSWD: /bin/mkdir,/bin/rm
```



## linux 文件常用命令

#### 创建文件

* touch，`touch file_test`
* cat，`cat file_test > file_test_copy` 或者 `cat file_test >> file_test_copy`
* echo，`echo "I love you" > file_test2`
* vi，`vi new_file`

#### 复制文件

* cat，`cat file_test > file-test_copy`
* cp，`cp file_test file_test_test2`

#### 链接文件

* ln -s 源文件 目标文件 （软链接，类似windows 的快捷方式）
* ln 源文件 目标文件（同样的文件，两个出口）

#### 重命名（移动）文件

mv 源文件 目标文件

#### 删除文件

rm

* -r recursive 递归删除，牵扯目录
* -f 



## linux 目录常用命令

### 创建目录

mkdir

-p 创建多级目录

```bash
mkdir test_dir/child_1/child_2/child_3
```



### 复制目录

cp -r 源目录 目标目录

-r 是递归的意思，必须要传

### 链接目录

#### 软链接

ln -s 源目录 目标目录（软连接）

#### 目录不可硬链接

### 重命名（移动）目录

mv 源目录 目标目录

### 删除目录

rm -r[f] 源目录 

-r 递归

-f 强制删除

rm -rf / 删除全部文件



## 查看文件信息

ls -l[hdi]

-l 以列表的形式展示

-h 优化文件大小的可读性

-i 展示文件的信息

-c 排序 按最后修改时间

文件信息 = 节点号 + 文件类型及权限 + 硬链接数 + 属主 + 所归属的组 + 文件或目录的大小 + 最近修改时间 + 文件或目录名

ls -ld */ 指数出目录，而不输出文件

文件类型及权限，第一个字符为d代表文件夹，为-表示是文件

## linux 统计技巧

grep 

"^d" 表示目录

"^-" 表示文件

wc （word count） -l（line）-c(character)

查看当前目录下的文件数量（不包含子目录中的文件）

```bash
ls -l | grep "^-" | wc
```

查看当前目录下的文件数量（包含子目录的文件），注意，R，代表递归子目录

```bash
ls -lR | grep "^-" | wc
```

使用通配符查询目录下的所有文件数量

```bash
ls -lR 20161124*/ | grep "^-" | wc -l
```

## Linux 文件属性

stat 命令（display file or file system status）

stat -f, —file-system

atime(Access time) 最后一次访问文件或目录的时间

mtime(modify time) 最后一次修改文件或目录的时间

ctime(change time) 最后一次改变文件或目录的时间

## Linux 文件类型

### 普通文件类型

* 纯文本文件
* 二进制文件
* 数据格式的文件
* 各种压缩文件
* 第一个属性为 [-]

### 目录文件

* 能用 # cd 命令进入的
* 第一个属性为 [d]，例如 [drwxrwxrwx]

### 块设备文件

* 就是存储数据以供系统存取的接口设备，简单而言就是硬盘。例如一号硬盘的代码时 /dev/hda1 等文件
* 第一个属性为 [b]

### 字符设备文件

* 串行端口的接口设备，例如键盘、鼠标等等。
* 第一个属性名为 [c]

### 套接字文件

* 这类文件通常用在网络数据链接。
* 可以启动一个程序来监听客户端的要求，客户端就可以通过套接字来进行数据通信。
* 第一个属性为 [s]
* 最常在 /var/run 目录中看到这种类型

### 管道文件

* FIFO 也是一种特殊的文件类型
* 它主要的目的是，解决多个程序同时存取一个文件所造成的错误
* 第一个属性为 [p]

### 链接文件

* 蕾丝windows 的快捷方式。
* 软链接，第一个属性为 [l]

## Linux 文件与目录查找技巧

find path [-option] [-print] [exec -ok command] {} \

```bash
# ls -l a*
# find -name a
# find -name "a*"
# find -name "*copy"
# find -name "a*" -type f
# find -name "a*" -type d
# find -name "a*" -type d -maxdepth 1
# find -maxdepth 3 -type f -exec rm -rf {} +
# find -maxdepth 1 -type f -exec cat {} +
```

* -name
* -perm 执行权限来查找
* -user 按文件属主来查找
* -group 按组来查找
* -ctime -1 一天之内创建的文件
* -mtime +1 一天之前修改的文件
* -atime 按文件的访问时间来查
* -nogroup 查找无有效属组的文件
* -nouser 查找无有效属主的文件
* -newer f1 !f2  查找更改时间比 f1 新 但比 f2 旧的文件
* -type b/d/c/p/l/f 块设备，目录，字符设备，管道，链接，文件
* -size n[c] 查找长度为 n 块或 n 字节的文件
* -depth 使查找在进入子目录之前先行查找完本目录
* -fstype 查找位于某一类型文件系统中的文件，这些文件系统类型通常可在 /etc/fstab中找到
* -mount 查找文件时，不跨越文件系统 mount 点
* -follow 如果遇到符号链接文件，就跟踪链接所指的文件
* -cpio 对匹配文件使用cpio 命令，将他们备份到磁带设备中
* -prune 忽略某个目录

