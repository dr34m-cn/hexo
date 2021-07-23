---
title: LocalDate获取当前周周一日期
date: 2021-06-21 13:40:24
tags: [编程,Java]
---

```java
import java.time.DayOfWeek;
import java.time.LocalDate;
import java.time.temporal.TemporalAdjusters;

LocalDate today = LocalDate.now();
LocalDate moday = today.with(TemporalAdjusters.previousOrSame(DayOfWeek.MONDAY));
```

