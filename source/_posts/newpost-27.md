---
title: element-ui 自带事件添加自定义参数
date: 2021-12-20 14:59:33
tags: 前端
---

## 目的

在使用`element-ui`自带事件，例如`@change`，添加自定义参数，并且保留原有参数

<!--more-->

## 方法

```js
@change='change($event,scope.row)'
```

其中`$event`就是自带的参数，`scope.row`替换成你想要的参数就可以了

或者也可以这样写

```js
@change='(val)=>change(value,scope.row)'
```

参考链接[element-ui @change添加自定义参数](https://www.cnblogs.com/knuzy/p/11097150.html)

