---
title: centos安装svn服务器
date: 2020-11-03 17:11:47
tags: 运维
toc: true
---

## 一、安装svn

```sh
yum install subversion
```
<!--more-->
## 二、建立版本库

```sh
cd /home/svn
mkdir project
svnadmin create /home/svn/project
```

## 三、控制权限、目录，启动

```sh
cd /home/svn/project/conf
```

authz 是权限控制文件 passwd 是帐号密码文件 svnserve.conf 是SVN服务配置文件

### 1.authz文件如下，`username`改为你的用户名

```
[/]
username = rw
```

可以添加多条，如

```
[/project1]
username1 = rw
```

### 2.passwd文件如下，`username`改为你的用户名，password改为密码，不用引号


```
[users]
username = password
username1 = password1
```

### 3.svnserve.conf文件如下

```
[general]
anon-access = none
auth-access = write
password-db = passwd
authz-db = authz
```

### 4.启动svn

```sh
svnserve -d -r /home/svn/project/
```

## 四、连接到SVN

安装诸如`svn小乌龟`之类的工具，连接到你的服务器ip加上`authz`文件中中括号内的路径，例如`svn://192.168.1.2/project1`，账号为`authz`文件中配置的账号`username1`，密码为`passwd`文件中配置的`password1`

## 五、重启SVN

```sh
killall svnserve
svnserve -d -r /home/svn/project/
```