---
title: nginx代理网站子目录到本地目录
date: 2020-11-12 13:54:49
tags: 运维
index_img: /assets/headImg/nginx.png
---

使用nginx的alias即可，例如下边的配置将子目录`static_web`代理到`/www/wwwroot/bg.ctftools.com`路径，访问`http://xxx.xx/static_web/index.html`即可达`/www/wwwroot/bg.ctftools.com/index.html`

```nginx
location ^~ /static_web {
    alias /www/wwwroot/bg.ctftools.com;
    index index.html;
    try_files $uri $uri/ /static_web/index.html;
}
```