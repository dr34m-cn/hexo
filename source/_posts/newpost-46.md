---
title: yum 更新 gcc
date: 2022-06-24 13:50:42
tags: [运维]
index_img: /assets/headImg/linux.png
---

# 目标

使用yum简单更新gcc，而不是编译安装

<!--more-->

# 实现

以下以更新到gcc7为例，更新到其他版本只需更改版本号即可

```sh
sudo yum install centos-release-scl
sudo yum install devtoolset-7-gcc*
scl enable devtoolset-7 bash
echo "source /opt/rh/devtoolset-7/enable" >>/etc/profile #添加这行长期使用
```
