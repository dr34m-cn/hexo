---
title: mysql获取当期日期是该年第几周
date: 2021-06-21 13:52:30
tags: [编程,MySQL]
index_img: /assets/headImg/mysql.png
---

* 最简单常用，返回值为4，这种情况按国际标准，周四在哪一年，这周就属于哪一年，默认周一是一周的第一天

```mysql
WEEKOFYEAR('2020-01-20')
```

<!--more-->

* 上边的等价于，但下边的返回值会带上年，如202004

```mysql
YEARWEEK('2020-01-20',1)
```

* 网上常见的方法

```mysql
WEEK(date_add('2020-01-20',interval 6 day),2)
```

