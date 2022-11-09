---
layout: post
title: "Docker环境下的前后端分离redis集群部署"
description: ""
category:
tags: [docker]
---

{% include JB/setup %}

## 安装 Redis，配置 RedisCluster 集群

### 1. 安装 Redis 镜像

```shell
docker pull yyyyttttwwww/redis
```

### 2. 创建 net2 网段

```shell
docker network create --subnet=172.19.0.0/16 net2
```

### 3. 创建 6 节点 Redis 容器

```shell
docker run -it -d --name r1 -p 5001:6379 --net=net2 --ip 172.19.0.2 redis bash
docker run -it -d --name r2 -p 5002:6379 --net=net2 --ip 172.19.0.3 redis bash
docker run -it -d --name r3 -p 5003:6379 --net=net2 --ip 172.19.0.4 redis bash
docker run -it -d --name r4 -p 5004:6379 --net=net2 --ip 172.19.0.5 redis bash
docker run -it -d --name r5 -p 5005:6379 --net=net2 --ip 172.19.0.6 redis bash
```

### 4. 启动 6 节点 Redis 服务器

```shell
#进入r1节点
docker exec -it r1 bash
cp /home/redis/redis.conf /usr/redis/redis.conf
cd /usr/redis/src
./redis-server ../redis.conf
#进入r2节点
docker exec -it r2 bash
cp /home/redis/redis.conf /usr/redis/redis.conf
cd /usr/redis/src
./redis-server ../redis.conf
#进入r3节点
docker exec -it r3 bash
cp /home/redis/redis.conf /usr/redis/redis.conf
cd /usr/redis/src
./redis-server ../redis.conf
#进入r4节点
docker exec -it r4 bash
cp /home/redis/redis.conf /usr/redis/redis.conf
cd /usr/redis/src
./redis-server ../redis.conf
#进入r5节点
docker exec -it r5 bash
cp /home/redis/redis.conf /usr/redis/redis.conf
cd /usr/redis/src
./redis-server ../redis.conf
#进入r6节点
docker exec -it r6 bash
cp /home/redis/redis.conf /usr/redis/redis.conf
cd /usr/redis/src
./redis-server ../redis.conf
```

### 5. 创建 Cluster 集群

```shell
#在r1节点上执行下面的指令
cd /usr/redis/src
mkdir -p ../cluster
cp redis-trib.rb ../cluster/
cd ../cluster
#创建Cluster集群
./redis-trib.rb create --replicas 1 172.19.0.2:6379 172.19.0.3:6379 172.19.0.4:6379 172.19.0.5:6379 172.19.0.6:6379 172.19.0.7:6379
```
