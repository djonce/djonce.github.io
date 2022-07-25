---
layout: post
title: "docker install nginx"
description: ""
category: 
tags: []
---
{% include JB/setup %}


### docker官方镜像仓库
地址： https://hub.docker.com

### 选择合适的nginx镜像版本
![搜索nginx镜像](/images/posts/docker/install-nginx-1.png)

![查看nginx版本](/images/posts/docker/install-nginx-2.png)


### 下载nginx镜像

| 命令     | 描述 |
| ----------- | ----------- |
| docker pull nginx      | 下载最新版本（等同于：docker pull nginx:latest       |
| docker pull nginx:版本号   | 下载制定版本       |


### 查看nginx镜像信息
```docker
docker images
```
![下载nginx镜像](/images/posts/docker/install-nginx-3.png)


### 创建nginx配置文件
```docker
docker run --name nginx -p 80:80 -d nginx
```

两种创建配置方式

#### 第一种参考nginx配置文档，纯手撸
地址：https://nginx.org/en/docs/http/configuring_https_servers.html

#### 第二种使用nginx默认配置文件
这里通过docker cp把容器里的nginx配置拷贝
```shell
docker cp nginx:/etc/nginx/nginx.conf /home/nginx/nginx.conf
docker cp nginx:/etc/nginx/conf.d /home/nginx/conf/
docker cp nginx:/usr/share/nginx/html /home/nginx/
```

### 创建Nginx容器并运行

```shell
docker run \
-p 80:80 \
-p 443:443 \
--name nginx \
-v /home/nginx/nginx.conf:/etc/nginx/nginx.conf \
-v /home/nginx/conf.d:/etc/nginx/conf.d \
-v /home/nginx/cert:/etc/nginx/cert \
-v /home/nginx/log:/var/log/nginx \
-v /home/nginx/html:/usr/share/nginx/html \
-d nginx:latest
```

### 验证结果

```shell
curl http://127.0.0.1
```

### 注意点

注：
1、站点目录，最好不要建在root目录，防止出现权限不足问题 
（"/root/wwwroot/demo.19ba.cn/index.html" is forbidden (13: Permission denied)）
2、将网站的目录挂载到容器，解决文件not found.
（*47 "/wwwroot/demo.19ba.cn/index.html" is not found (2: No such file or directory)）

