---
title: 常见nginx反向代理配置
date: 2020-11-13 10:20:28
tags: [运维,Nginx]
---

* 此配置可以用于jenkins反向代理等

```nginx
location / {
    proxy_pass http://127.0.0.1:8888;
    proxy_http_version  1.1;
    proxy_cache_bypass  $http_upgrade;
    proxy_set_header Upgrade           $http_upgrade;
    proxy_set_header Connection        "upgrade";
    proxy_set_header Host              $host;
    proxy_set_header X-Real-IP         $remote_addr;
    proxy_set_header X-Forwarded-For   $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
    proxy_set_header X-Forwarded-Host  $host;
    proxy_set_header X-Forwarded-Port  $server_port;
}
```

