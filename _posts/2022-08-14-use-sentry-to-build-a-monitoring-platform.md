---
layout: post
title: "使用sentry搭建监控平台"
description: ""
category:
tags: []
---

{% include JB/setup %}

## 前言

Sentry 是一个开源的实时错误监控系统。

## 部署

### 1、拉取镜像

```
docker run -d --name sentry-redis --restart=always redis
docker run -d --name sentry-postgres -e POSTGRES_PASSWORD=wangjie -e POSTGRES_USER=sentry --restart=always postgres
```

### 2、启动数据库服务

```
docker run -d --name sentry-redis --restart=always redis
docker run -d --name sentry-postgres -e POSTGRES_PASSWORD=secret -e POSTGRES_USER=sentry --restart=always postgres
```

### 3、生成 sentry 秘钥

```
docker run --rm sentry config generate-secret-key
eg: z0v!)p2^mdyr3kl+qz3vp9=h2y4vfbtljsk#55pxbu#4^t2#-y
```

### 4、数据库及账户初始化

```
docker run -it --rm -e SENTRY_SECRET_KEY=<your generate-secret-key> --link sentry-postgres:postgres --link sentry-redis:redis sentry upgrade
```

### 5、启动 sentry 的 web 服务

```
docker run -d -p 9000:9000 --name my-sentry -e SENTRY_SECRET_KEY=<your generate-secret-key> --link sentry-redis:redis --link sentry-postgres:postgres --restart=always sentry
```

### 6、启动 sentry-cron/work 服务

```
docker run -d --name sentry-cron -e SENTRY_SECRET_KEY=<your generate-secret-key> --link sentry-postgres:postgres --link sentry-redis:redis sentry run cron
docker run -d --name sentry-worker-1 -e SENTRY_SECRET_KEY=<your generate-secret-key> --link sentry-postgres:postgres --link sentry-redis:redis sentry run worker
```

### 域名配置

nginx 配置

```
server{
   listen 80;
   server_name ms.19ba.cn;

   rewrite ^(.*)$ https://$host$1  permanent;
}

server {
    #SSL 访问端口号为 443
    listen 443 ssl;
    #填写绑定证书的域名
    server_name ms.19ba.cn;
    #证书文件名称，容器中的路径
    ssl_certificate /etc/nginx/cert/ms.19ba.cn.pem;
    #私钥文件名称， 容器中的路径
    ssl_certificate_key /etc/nginx/cert/ms.19ba.cn.key;
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
        proxy_set_header Host $host;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection upgrade;
        proxy_set_header Accept-Encoding gzip;
        proxy_pass http://your host:9000;
    }
    #
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }
   }

```

## 访问地址

[https://ms.19ba.cn](https://ms.19ba.cn)

参考：https://hub.docker.com/_/sentry
