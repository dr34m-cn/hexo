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
/a.jpg          表示仅仅忽略项目根目录下的 a.jpg 文件，不包括 /abc/a.jpg
data/:          表示忽略data目录下的所有内容，不忽略 data 文件
/data:          表示忽略根目录下的data文件
/*.pdf:         表示忽略/a.pdf，不忽略 /doc/a.pdf
**/a.txt:       表示忽略/a.txt,a/a.txt,a/b/a.txt等
a/**/b.txt:     表示忽略a/b.txt, a/x/b.txt,a/x/y/b.txt等
/mtk/do.doc     表示过滤某个具体文件
fd1/*           表示忽略/fd1/，/fd0/df1/等下所有文件
/fd1/*          表示忽略/fd1/下所有文件，不包括/fd0/df1/
```

