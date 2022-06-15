---
layout: post
title: "node project package docker images"
description: ""
category: 
tags: []
---
{% include JB/setup %}


### 前言

### node项目


### 简单的脚本
简单将本地已经安装，可以运行的项目`直接打包成镜像`

```shell
FROM node:12

RUN mkdir -p /usr/ocr/app/

WORKDIR /usr/ocr/app/

COPY . /usr/ocr/app/

EXPOSE 3000

CMD npm start

```

### 构建镜像

```shell
docker build . -t ocr

```

### 查看镜像构建成功
```shell
docker images
```

![查看镜像详情](/images/posts/docker/node-package-image-1.png)

