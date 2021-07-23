---
title: Python自动处理依赖
date: 2019-11-14 20:45:54
tags: 编程
---

### 一、起由

python项目常常引入众多包，当更换环境时一个个导入太麻烦，而且有导入错版本导致不兼容的可能，本文提供了一种非常常用的方法自动处理python依赖。
<!--more-->

### 二、生成依赖（以下操作需要先cmd到项目根目录）

#### 1.生成当前python环境所有依赖

一条命令即可：
```bash
pip freeze > requirements.txt
```
#### 2.只生成当前项目所所需依赖

```bash
pip install pipreqs
pipreqs ./
```
如果报编码错误那么强制使用utf-8
```bash
pipreqs ./ --encoding=utf-8
```

### 三、安装依赖

一条命令即可：
```bash
pip install -r requirements.txt
```
