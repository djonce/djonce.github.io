---
layout: post
title: "oprnfire install"
description: ""
category: 
tags: []
---
{% include JB/setup %}


## 安装

Dockerhub上提供了映像的自动构建，这是推荐的安装方法。
```
docker pull nasqueron/openfire:4.7.1
```
或者，您可以自己构建图像。
```
docker build -t nasqueron/openfire github.com/nasqueron/docker-openfire
```

## 快速开始
使用以下命令启动 Openfire：
```
docker run --name openfire -d --restart=always \
  --publish 9090:9090 --publish 5222:5222 --publish 7777:7777 \
  --volume /srv/docker/openfire:/var/lib/openfire \
  nasqueron/openfire:4.7.1
```
或者，您可以使用示例docker-compose.yml文件来使用Docker Compose启动容器

将浏览器指向http://localhost:9090并按照设置过程完成安装。HAKK5 的在 10 分钟内构建免费的 Jabber 服务器视频应该可以帮助您进行配置，还可以向您介绍它的一些功能。

## 持久性
为了让 Openfire 在容器关闭和启动期间保持其状态，您应该在/var/lib/openfire.

快速启动命令已经安装了一个卷以进行持久化。

SELinux 用户应该更新主机挂载点的安全上下文，以便它可以很好地与 Docker 配合使用：
```
mkdir -p /srv/docker/openfire
chcon -Rt svirt_sandbox_file_t /srv/docker/openfire
```
Java 虚拟机选项
您可以将选项附加到启动命令以配置 JVM：
```
docker run -name openfire -d \
  [DOCKER_OPTIONS] \
  nasqueron/openfire:4.7.1 \
  -XX:+UseConcMarkSweepGC -XX:+CMSIncrementalMode
```

## 日志
要访问位于 的 Openfire 日志，**/var/log/openfire**您可以使用docker exec. 例如，如果您想跟踪日志：
```
docker exec -it openfire tail -f /var/log/openfire/info.log
```

## 维护
### 升级
要升级到较新版本：

**1.下载更新的 Docker 镜像：**
```
docker pull nasqueron/openfire:4.7.1
```

**2.停止当前运行的镜像：**
```
docker stop openfire
```
**3.移除停止的容器**
```
docker rm -v openfire
```

**4.开始更新镜像**
```
docker run -name openfire -d \
  [OPTIONS] \
  nasqueron/openfire:4.7.1
```

###  外壳访问
出于调试和维护目的，您可能需要访问容器外壳。如果您使用的是 Docker 版本或更高版本，则可以通过开始使用1.3.0来访问正在运行的容器外壳：bashdocker exec
```
docker exec -it openfire bash
```