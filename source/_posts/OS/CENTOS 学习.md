---
title: CENTOS 7 学习
date: 
categories:
- 运维
tags:
- linux
- CentOS
- 待续
---

# CentOS 7

安装好之后，配置 yum 源

vim /etc/yum.repos.d/CentOS-163.repo，并且将 yum 文件复制进去



## 系统管理配置

### 主机名修改

#### 临时修改

```bash
hostname [新主机名]
```

#### 永久修改

修改这个就无效了

```bash
vim /etc/sysconfig/network
```

要修改下面的文件 

```bash
vim /ect/hostname
```

### 文件目录结构

 ![image-20191109062349992](/Users/lvjiawen/Library/Application Support/typora-user-images/image-20191109062349992.png)

/home 普通用户的 ~ 目录，root 用户的 家目录是 /root

/etc 系统和应用的相关配置文件

bin 和 sbin 链接到 usr/bin 和 usr/sbin 可执行命令 ls 之类的都在这儿，sbin 是 super bin，root 用户执行的目录

/boot 操作系统启动的引导文件

lib 和 lib64 链接到 usr/lib64和usr/lib 系统的库文件

/tmp 和 /run 临时文件，会定时清理

/var 系统日志，帮助文档等等，还有一些系统的库文件

/proc 系统进程的动态信息

/dev 系统的设备文件



### 操作系统的时区修改

![image-20191109063942086](/Users/lvjiawen/Library/Application Support/typora-user-images/image-20191109063942086.png)

`date -R` 查看时区

RTC 主板时间

`timedatectl` 查看时间

```bash
timedatectl set-timezone Asia/Shangehai // 修改操作系统的时间
timedatectl set-local-rtc 1 // 将硬件的时间跟系统的时间保持一致
timedatectl list-timezone
```

###  操作系统网卡命名

网卡1 eth0 em0

网卡2 eth1 em1

网卡3 eth2 em2

centos7 网卡命名默认是基于固件、拓扑、位置信息来分配，想要关闭它，配置

```bash
vim /etc/sysconfig/grub
grub2-mkconfig -o /boot/grub2/grub.cfg
```

修改

```bash
GRUB_CMDLINE_LINUX=
....
net.ifnames=0 biosdevname=0

...
```

阿里云不建议对网卡进行设置

查看网卡信息

```bash
ip a 
```



### 网络接口及 IP 详解



## 命令和其他

ls -l ./ 打印出文件详细信息

pid 是进程的id 的意思

