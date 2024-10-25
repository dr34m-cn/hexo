---
title: pyinstaller打包的程序执行报错Failed to extract xxxx decompression resulted in return code -1
tags:
  - Python
  - 编程
index_img: /assets/headImg/python.png
date: 2024-10-25 09:55:32
---

## 报错

`Failed to extract xxxx: decompression resulted in return code -1!`

## 原因

系统盘（`windows`）或`tmp`目录（`linux`）空间不够了

## 原理

`pyinstaller`打包的程序在执行时会默认解压临时文件到一个临时目录（是一个`_MEI`开头的随机目录）下来执行，执行完成后会删除这个目录。在windows下，这个目录在`%TEMP%`下；`linux`在`/tmp`下

[原理官方文档](https://pyinstaller.org/en/stable/operating-mode.html#how-the-one-file-program-works)

## 解决方法

### 方法1. 释放空间或扩容

无需解释

### 方法2. 修改默认解压目录

例如解压到`/mnt/tmp`

#### 2.1. 打包命令加上`--runtime-tmpdir`参数

`pyinstaller --runtime-tmpdir /mnt/tmp main.py`

#### 2.2. 修改.spec文件

```spec
......
exe = EXE(
    pyz,
    ......
    runtime_tmpdir='/mnt/tmp',
    ......
```

[参数官方文档](https://pyinstaller.org/en/stable/usage.html#cmdoption-runtime-tmpdir)
