---
title: svn提交后jenkins自动部署
date: 2020-11-13 14:53:49
tags: [运维,持续集成]
---

## 一、jenkins相关

### 1.创建SVN专用账号

进入`jenkins`管理后台，`系统管理`-`管理用户`-左边的`新建用户`，输入相关信息后点击`新建用户`，假设你创建的账号密码分别是`svn`，`svnpwd`。

<!--more-->

* 此步骤可以省略，给`SVN`创建专用账户的好处是可以知道该条部署是由`SVN`自动部署，还是某人手工部署。

### 2.命令部署SVN

进入`jenkins`管理后台，`系统管理`-`Jenkins 命令行接口`，在页面第一行点击下载`jenkins-cli.jar`，并复制到SVN所在服务器的某个目录，例如`/home/svn/jenkins-cli.jar`，在此之前你的服务器应当已经安装了jdk环境。假设你的`jenkins`运行在`http://127.0.0.1:8080`，将要部署的项目名为`blog`，通过以下命令测试是否可以完成部署。

```bash
java -jar /home/svn/jenkins-cli.jar -s http://127.0.0.1:8080 -auth svn:svnpwd build blog
```

* 注意，此命令需要`jenkins`使用`webSocket`，如果你使用不完整的反向代理配置可能导致该方法不可用。

## 二、SVN相关

### 1.使用post-commit

进入你的`SVN`项目目录，例如`/home/svn/project`，可以看到hooks文件夹，进入该文件夹，复制`post-commit.tmpl`到当前目录命名为`post-commit`，并赋予其执行权限

```bash
chmod +x post-commit
```

清空里边的内容并编辑如下

```bash
#!/bin/sh
REPOS="$1"
REV="$2"
source /etc/profile
AUTHOR=$(svnlook author -r $REV $REPOS)
echo $AUTHOR >> /home/svn/project/hooks/user.txt
exit 0
```

测试提交代码，如果可以看到提交代码的用户名已经打印到`/home/svn/project/hooks/user.txt`文件中，则说明成功。

* 提示`post-commit hook failed (exit code 255) with no output`多半是没有赋予`post-commit`可执行权限，还有可能下边的原因。我之前没有复制`post-commit.tmpl`而是选择手动新建了`post-commit`文件，就一直报错，百撕不得骑姐，后来复制`post-commit.tmpl`就没问题了。

* 添加`source /etc/profile`的目的是为了确保jdk环境正常。

### 2.配置命令自动部署

修改`post-commit`文件为

```bash
#!/bin/sh
source /etc/profile
java -jar /home/svn/jenkins-cli.jar -s http://127.0.0.1:8080 -auth svn:svnpwd build blog
exit 0
```

这样配置只要提交代码就会操作`jenkins`部署。如果需要指定用户提交代码才部署则可以如下

```bash
#!/bin/sh
REPOS="$1"
REV="$2"
source /etc/profile
AUTHOR=$(svnlook author -r $REV $REPOS)
if [ $AUTHOR == admin ];then
    java -jar /home/svn/jenkins-cli.jar -s http://127.0.0.1:8080 -auth svn:svnpwd build blog
fi
exit 0
```

