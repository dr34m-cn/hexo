---
title: taoSync排除项简易教程
tags:
  - 经验
  - 文档
index_img: /assets/headImg/tao-alist-docker.png
date: 2024-09-26 10:59:43
---

`taoSync`的排除项规则是一种类似`gitignore`的规则，但不支持[非]的语法（即`!`），优先级未得到正确支持（详见[这里](https://github.com/bitranox/igittigitt?tab=readme-ov-file#limitations)）;

`taoSync`规定的排除项根目录为同步来源或目标目录，例如你的来源目录为`/baidu/pan/`，使用规则`/123321/*`将忽略目录`/baidu/pan/123321/`下的文件；使用`/baidu/*`不会有效果。

<!--more-->

### 规则简单说明

```gitignore
.*              表示忽略所有 . 开头的文件和目录
*.a             表示忽略所有 .a 结尾的文件
/a.jpg          表示仅仅忽略项目根目录下的 a.jpg 文件，不包括 subdir/a.jpg
bin/:           表示忽略当前路径下的bin文件夹，该文件夹下的所有内容都会被忽略，不忽略 bin 文件
/bin:           表示忽略根目录下的bin文件
/*.c:           表示忽略cat.c，不忽略 build/cat.c
debug/*.obj:    表示忽略debug/io.obj，不忽略 debug/common/io.obj和tools/debug/io.obj
**/foo:         表示忽略/foo,a/foo,a/b/foo等
a/**/b:         表示忽略a/b, a/x/b,a/x/y/b等
*.log:          表示忽略所有 .log 文件
/mtk/do.c       表示过滤某个具体文件

----------------------------------------------------------------------------------
还有一些规则如下：
fd1/*
说明：忽略目录 fd1 下的全部内容；注意，不管是根目录下的 /fd1/ 目录，还是某个子目录 /child/fd1/ 目录，都会被忽略；
 
/fd1/*
说明：忽略根目录下的 /fd1/ 目录的全部内容；

```

