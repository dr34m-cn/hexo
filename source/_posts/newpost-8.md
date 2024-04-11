---
title: Java过滤html标签
date: 2020-12-28 14:26:04
tags: [编程,Java]
index_img: /assets/headImg/java.png
---

假设str为html富文本，则

```java
str.replaceAll("\\&[a-zA-Z]{1,10};", "").replaceAll("<[^>]*>", "").replaceAll("[(/>)<]", "")
```