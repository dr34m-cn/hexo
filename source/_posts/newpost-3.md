---
title: mysql获取最近一段时间数据
date: 2020-11-26 16:25:56
tags: [编程,MySQL]
index_img: /assets/headImg/mysql.png
---

* 获取最近七天，其中`CREATE_TIME`改为实际表中存储时间的字段

```mysql
select count(id) from table where date_sub(curdate(), interval 7 day) <= date(CREATE_TIME)
```

<!--more-->

* 获取最近半年

```mysql
select count(id) from table where date_sub(curdate(), interval 6 month) <= date(CREATE_TIME)
```

* 获取最近一年

```mysql
select count(id) from table where date_sub(curdate(), interval 1 year) <= date(CREATE_TIME)
```

* 获取当月

```mysql
select count(id) from table where DATE_FORMAT(NOW(),'%Y-%m') == DATE_FORMAT(CREATE_TIME,'%Y-%m')
```
