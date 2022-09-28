---
layout: post
title: "docker install nginx"
description: ""
category:
tags: [docker]
---

{% include JB/setup %}

### docker 官方镜像仓库

地址： https://hub.docker.com

### 选择合适的 nginx 镜像版本

![搜索nginx镜像](/images/posts/docker/install-nginx-1.png)

![查看nginx版本](/images/posts/docker/install-nginx-2.png)

### 下载 nginx 镜像

| 命令                     | 描述                                           |
| ------------------------ | ---------------------------------------------- |
| docker pull nginx        | 下载最新版本（等同于：docker pull nginx:latest |
| docker pull nginx:版本号 | 下载制定版本                                   |

### 查看 nginx 镜像信息

```docker
docker images
```

![下载nginx镜像](/images/posts/docker/install-nginx-3.png)

### 创建 nginx 配置文件

```docker
docker run --name nginx -p 80:80 -d nginx
```

两种创建配置方式

#### 第一种参考 nginx 配置文档，纯手撸

地址：https://nginx.org/en/docs/http/configuring_https_servers.html

#### 第二种使用 nginx 默认配置文件

这里通过 docker cp 把容器里的 nginx 配置拷贝

```shell
docker cp nginx:/etc/nginx/nginx.conf /home/nginx/nginx.conf
docker cp nginx:/etc/nginx/conf.d /home/nginx/conf.d
docker cp nginx:/usr/share/nginx/html /home/nginx/html
```

### 创建 Nginx 容器并运行

```shell
docker run \
-p 80:80 \
-p 443:443 \
--name nginx \
-v /docker/nginx/nginx.conf:/etc/nginx/nginx.conf \
-v /docker/nginx/conf.d:/etc/nginx/conf.d \
-v /docker/nginx/certs:/etc/nginx/certs \
-v /docker/nginx/log:/var/log/nginx \
-v /docker/nginx/html:/usr/share/nginx/html \
-d nginx:latest
```

### 验证结果

```shell
curl http://127.0.0.1
```

### 注意点

注：

1、站点目录，最好不要建在 **root** 目录，防止出现权限不足问题
（"/root/wwwroot/demo.19ba.cn/index.html" is forbidden (13: Permission denied)）

2、将网站的目录挂载到容器，解决文件 not found.
（\*47 "/wwwroot/demo.19ba.cn/index.html" is not found (2: No such file or directory)）
