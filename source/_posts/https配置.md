---
title: https配置
date: 2017-07-31 11:01:28
tags:
  - https
  - 学习
categories: 全栈
---

# https配置

```
server {
    listen          80;
    server_name     hr.dduke.me;
    rewrite ^(.*) https://$host$1 permanent;
}
server {
    listen          443;
    server_name     hr.dduke.me;
    #rewrite ^(.*) https://$host$1 permanent;
    ssl on;
    ssl_certificate 1_hr.dduke.me_bundle.crt;
    ssl_certificate_key 2_hr.dduke.me.key;
    ssl_session_timeout 5m;
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2; #按照这个协议配置
    ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE;#按照这个套件配置
    ssl_prefer_server_ciphers on;
    location / {
        proxy_pass http://127.0.0.1:8082;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```
