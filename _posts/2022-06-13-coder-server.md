---
layout: post
title: "coder server搭建"
description: ""
category: 
tags: []
---
{% include JB/setup %}

## 搭建

通过docker镜像来快速搭建

``` docker
  docker run -d --name code-server -p 8070:8080 \
  -v "$PWD/.config:/home/coder/.config" \
  -v "$PWD/code-server:/home/coder/project" \
  -u "$(id -u):$(id -g)" \
  -e "DOCKER_USER=$USER" \
  codercom/code-server:latest
```
