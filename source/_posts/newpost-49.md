---
title: VUE实现复制与粘贴_获取剪切板内容
date: 2022-09-01 09:28:23
tags: [前端, Vue]
index_img: /assets/headImg/vue-copy.png
---

# 目标

在VUE项目中实现复制与粘贴。

<!--more-->

# 实现

## 1. 复制

### 安装

```sh
npm install --save v-clipboard
```

`main.js`

```js
import Vue from 'vue'
import Clipboard from 'v-clipboard'
 
Vue.use(Clipboard)
```

### 使用

```js
this.$clipboard(value);
```

> 更多使用方式可参见文档：[https://github.com/euvl/v-clipboard](https://github.com/euvl/v-clipboard)

## 2. 粘贴

粘贴的本质是获取剪切板内容，然后赋值给需要粘贴的地方，获取剪切板内容可以使用下边的方法：

```js
navigator.clipboard.readText().then((res) => {
    console.log('剪切板内容:', res);
}).catch((err) => {
    console.log('获取剪切板内容失败:', err);
});
```



