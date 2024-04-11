---
title: JavaScript获取当前周或下n周的周n的日期
date: 2020-12-15 17:32:57
tags: [JavaScript,前端]
index_img: /assets/headImg/js.png
---

调用方法`getDate(cur_m, day_m)`

<!--more-->

```js
function getDate(cur_m, day_m) {
  // cur_m，0-本周，1-下周，2-下下周...
  // day_m，1-周一，2-周二...7-周日
  var date = new Date();
  var day = date.getDay() || 7;
  date = new Date(date.getFullYear(), date.getMonth(), date.getDate() - day + day_m + 7 * cur_m);
  var y = date.getFullYear();
  var m = date.getMonth() + 1;
  m = m < 10 ? '0' + m : m;
  var d = date.getDate();
  d = d < 10 ? ('0' + d) : d;
  return y + '-' + m + '-' + d;
}
```

