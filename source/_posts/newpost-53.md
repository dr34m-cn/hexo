---
title: 编写bat脚本实现对vue项目构建并压缩
date: 2023-09-07 16:26:15
tags: [前端,运维]
index_img: /assets/headImg/vue.png
---

# 目标

编写bat脚本自动完成代码更新、构建、压缩为日期命名的压缩包

<!--more-->

# 实现

## 一、准备

### 1、npm install

通常`npm install`只要执行一次，就不写在脚本里了，所以需要预先执行过一次`npm install`

### 2、压缩工具

windows下需要一个压缩工具来实现命令压缩

[官网](http://www.7-zip.org/download.html)下载安装文件，安装完成后把安装目录的`7z.exe`和`7z.dll`复制到`C:\Windows\System32`目录下，如果不需要7z就可以卸载了

## 二、脚本

项目根目录新建`打包.bat`文件，并编写如下

```bat
set "curr_date=%date%"
set "yy=%curr_date:~0,4%"
set "mn=%curr_date:~5,2%"
set "dd=%curr_date:~8,2%"
set "curr_time=%time%"
if %curr_time:~0,2% leq 9 (set hh=0%curr_time:~1,1%) else (set hh=%curr_time:~0,2%)
set "mm=%curr_time:~3,2%"
set "ss=%curr_time:~6,2%"

set "newname=%yy%%mn%%dd%-%hh%%mm%%ss%.zip"

git pull && npm run build && 7z a %newname% dist
```

上边的名称可以自定义修改为自己想要的，例如改为`newname=%yy%%mn%%dd%-%hh%%mm%%ss%-prod.zip`

如果想要压缩包根目录不是dist目录，直接是dist目录中的文件，最后一行可以改为

```bat
git pull && npm run build && cd dist && 7z a %newname% **
```