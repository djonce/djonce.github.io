---
layout: post
title: "https证书申请"
description: ""
category: 
tags: []
---
{% include JB/setup %}


### 前言

### 免费申请HTTPS证书
https://zhuanlan.zhihu.com/p/138792764

### 配置
```
server {
    #SSL 访问端口号为 443
    listen 443 ssl;
    #填写绑定证书的域名
    server_name api.19ba.cn;
    #证书文件名称，容器中的路径
    ssl_certificate /etc/nginx/cert/fullchain.cer;
    #私钥文件名称， 容器中的路径
    ssl_certificate_key /etc/nginx/cert/cert.key;
    ssl_session_timeout 5m;
    #请按照以下协议配置
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    #请按照以下套件配置，配置加密套件，写法遵循 openssl 标准。
    ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE;
    ssl_prefer_server_ciphers on;
    location / {
    #网站主页路径。此路径仅供参考，具体请您按照实际目录操作。
        # root /usr/share/nginx/html;
        # index  index.html;
        proxy_pass http://xxx:3000;
    }

    #access_log  /var/log/nginx/host.access.log  main;

    #error_page  404              /404.html;

    # redirect server error pages to the static page /50x.html
    #
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }
}


```

### 多域名配置

### 验证配置

```shell
  openssl s_client -connect www.example.com:443
```