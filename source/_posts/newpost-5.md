---
title: java复制不同实体类中相同的字段
date: 2020-12-03 17:50:16
tags: [编程,Java]
index_img: /assets/headImg/java.png
---

用下边的方法就能实现，其中`source`为复制源，`target`为复制目标。

```java
import org.springframework.beans.BeanUtils;

BeanUtils.copyProperties(source, target);
```
