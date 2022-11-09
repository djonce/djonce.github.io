---
layout: post
title: "Docker环境下的前后端分离mysql集群部署"
description: ""
category:
tags: [docker]
---

{% include JB/setup %}

## 安装 PXC 集群，负载均衡，双机热备

### 1. 安装 PXC 镜像

```shell
docker pull percona/percona-xtradb-cluster
```

### 2. 为 PXC 镜像改名

```shell
docker tag percona/percona-xtradb-cluster pxc
```

### 3. 创建 net1 网段

```shell
docker network create --subnet=172.18.0.0/16 net1
```

### 4. 创建 5 个数据卷

```shell
docker volume create --name v1
docker volume create --name v2
docker volume create --name v3
docker volume create --name v4
docker volume create --name v5
```

### 5. 创建备份数据卷（用于热备份数据）

```shell
docker volume create --name backup
```

### 6. 创建 5 节点的 PXC 集群

注意，每个 MySQL 容器创建之后，因为要执行 PXC 的初始化和加入集群等工作，耐心等待 1 分钟左右再用客户端连接 MySQL。另外，必须第 1 个 MySQL 节点启动成功，用 MySQL 客户端能连接上之后，再去创建其他 MySQL 节点。

```shell
#创建第1个MySQL节点
docker run -d -p 3306:3306 -e MYSQL_ROOT_PASSWORD=abc123456 -e CLUSTER_NAME=PXC -e XTRABACKUP_PASSWORD=abc123456 -v v1:/var/lib/mysql -v backup:/data --privileged --name=node1 --net=net1 --ip 172.18.0.2 pxc
#创建第2个MySQL节点
docker run -d -p 3307:3306 -e MYSQL_ROOT_PASSWORD=abc123456 -e CLUSTER_NAME=PXC -e XTRABACKUP_PASSWORD=abc123456 -e CLUSTER_JOIN=node1 -v v2:/var/lib/mysql -v backup:/data --privileged --name=node2 --net=net1 --ip 172.18.0.3 pxc
#创建第3个MySQL节点
docker run -d -p 3308:3306 -e MYSQL_ROOT_PASSWORD=abc123456 -e CLUSTER_NAME=PXC -e XTRABACKUP_PASSWORD=abc123456 -e CLUSTER_JOIN=node1 -v v3:/var/lib/mysql --privileged --name=node3 --net=net1 --ip 172.18.0.4 pxc
#创建第4个MySQL节点
docker run -d -p 3309:3306 -e MYSQL_ROOT_PASSWORD=abc123456 -e CLUSTER_NAME=PXC -e XTRABACKUP_PASSWORD=abc123456 -e CLUSTER_JOIN=node1 -v v4:/var/lib/mysql --privileged --name=node4 --net=net1 --ip 172.18.0.5 pxc
#创建第5个MySQL节点
docker run -d -p 3310:3306 -e MYSQL_ROOT_PASSWORD=abc123456 -e CLUSTER_NAME=PXC -e XTRABACKUP_PASSWORD=abc123456 -e CLUSTER_JOIN=node1 -v v5:/var/lib/mysql -v backup:/data --privileged --name=node5 --net=net1 --ip 172.18.0.6 pxc
```

### 7. 安装 Haproxy 镜像

```shell
docker pull haproxy
```

### 8. 宿主机上编写 Haproxy 配置文件

```shell
vi /home/soft/haproxy.cfg
```

配置文件如下：

```properties
global
	#工作目录
	chroot /usr/local/etc/haproxy
	#日志文件，使用rsyslog服务中local5日志设备（/var/log/local5），等级info
	log 127.0.0.1 local5 info
	#守护进程运行
	daemon

defaults
	log	global
	mode	http
	#日志格式
	option	httplog
	#日志中不记录负载均衡的心跳检测记录
	option	dontlognull
  #连接超时（毫秒）
	timeout connect 5000
  #客户端超时（毫秒
	timeout client  50000
	#服务器超时（毫秒）
  timeout server  50000

#监控界面
listen  admin_stats
	#监控界面的访问的IP和端口
	  bind  0.0.0.0:8888
	#访问协议
    mode        http
	#URI相对地址
    stats uri   /dbs
	#统计报告格式
    stats realm     Global\ statistics
	#登陆帐户信息
    stats auth  admin:abc123456
  #数据库负载均衡
listen  proxy-mysql
	#访问的IP和端口
	bind  0.0.0.0:3306
  #网络协议
	mode  tcp
	#负载均衡算法（轮询算法）
	#轮询算法：roundrobin
	#权重算法：static-rr
	#最少连接算法：leastconn
	#请求源IP算法：source
    balance  roundrobin
	#日志格式
    option  tcplog
	#在MySQL中创建一个没有权限的haproxy用户，密码为空。Haproxy使用这个账户对MySQL数据库心跳检测
    option  mysql-check user haproxy
    server  MySQL_1 172.18.0.2:3306 check weight 1 maxconn 2000
    server  MySQL_2 172.18.0.3:3306 check weight 1 maxconn 2000
	server  MySQL_3 172.18.0.4:3306 check weight 1 maxconn 2000
	server  MySQL_4 172.18.0.5:3306 check weight 1 maxconn 2000
	server  MySQL_5 172.18.0.6:3306 check weight 1 maxconn 2000
	#使用keepalive检测死链
    option  tcpka
```

### 9. 创建两个 Haproxy 容器

```shell
#创建第1个Haproxy负载均衡服务器
docker run -it -d -p 4001:8888 -p 4002:3306 -v /home/soft/haproxy:/usr/local/etc/haproxy --name h1 --privileged --net=net1 --ip 172.18.0.7 haproxy
#进入h1容器，启动Haproxy
docker exec -it h1 bash
haproxy -f /usr/local/etc/haproxy/haproxy.cfg
#创建第2个Haproxy负载均衡服务器
docker run -it -d -p 4003:8888 -p 4004:3306 -v /home/soft/haproxy:/usr/local/etc/haproxy --name h2 --privileged --net=net1 --ip 172.18.0.8 haproxy
#进入h2容器，启动Haproxy
docker exec -it h2 bash
haproxy -f /usr/local/etc/haproxy/haproxy.cfg
```

### 10. Haproxy 容器内安装 Keepalived，设置虚拟 IP

    ```shell
    #进入h1容器
    docker exec -it h1 bash
    #更新软件包
    apt-get update
    #安装VIM
    apt-get install vim
    #安装Keepalived
    apt-get install keepalived
    #编辑Keepalived配置文件（参考下方配置文件）
    vim /etc/keepalived/keepalived.conf
    #启动Keepalived
    service keepalived start
    #宿主机执行ping命令
    ping 172.18.0.201
    ```

    配置文件内容如下：

    ```
    vrrp_instance  VI_1 {
        state  MASTER
        interface  eth0
        virtual_router_id  51
        priority  100
        advert_int  1
        authentication {
            auth_type  PASS
            auth_pass  123456
        }
        virtual_ipaddress {
            172.18.0.201
        }
    }
    ```

    ```shell
    #进入h2容器
    docker exec -it h2 bash
    #更新软件包
    apt-get update
    #安装VIM
    apt-get install vim
    #安装Keepalived
    apt-get install keepalived
    #编辑Keepalived配置文件
    vim /etc/keepalived/keepalived.conf
    #启动Keepalived
    service keepalived start
    #宿主机执行ping命令
    ping 172.18.0.201
    ```

    配置文件内容如下：

    ```shell
    vrrp_instance  VI_1 {
        state  MASTER
        interface  eth0
        virtual_router_id  51
        priority  100
        advert_int  1
        authentication {
            auth_type  PASS
            auth_pass  123456
        }
        virtual_ipaddress {
            172.18.0.201
        }
    }
    ```

### 11. 宿主机安装 Keepalived，实现双击热备

    ```shell
    #宿主机执行安装Keepalived
    yum -y install keepalived
    #修改Keepalived配置文件
    vi /etc/keepalived/keepalived.conf
    #启动Keepalived
    service keepalived start
    ```

    Keepalived 配置文件如下：

    ```shell
    vrrp_instance VI_1 {
        state MASTER
        interface ens33
        virtual_router_id 51
        priority 100
        advert_int 1
        authentication {
            auth_type PASS
            auth_pass 1111
        }
        virtual_ipaddress {
           	192.168.99.150
        }
    }

    virtual_server 192.168.99.150 8888 {
        delay_loop 3
        lb_algo rr
        lb_kind NAT
        persistence_timeout 50
        protocol TCP

        real_server 172.18.0.201 8888 {
            weight 1
        }
    }

    virtual_server 192.168.99.150 3306 {
        delay_loop 3
        lb_algo rr
        lb_kind NAT
        persistence_timeout 50
        protocol TCP

        real_server 172.18.0.201 3306 {
            weight 1
        }
    }
    ```

### 12. 热备份数据

    ```shell
    #进入node1容器
    docker exec -it node1 bash
    #更新软件包
    apt-get update
    #安装热备工具
    apt-get install percona-xtrabackup-24
    #全量热备
    innobackupex --user=root --password=abc123456 /data/backup/full
    ```

### 13. 冷还原数据

    停止其余 4 个节点，并删除节点

    ```shell
    docker stop node2
    docker stop node3
    docker stop node4
    docker stop node5
    docker rm node2
    docker rm node3
    docker rm node4
    docker rm node5
    ```

    node1 容器中删除 MySQL 的数据

    ```shell
    #删除数据
    rm -rf /var/lib/mysql/*
    #清空事务
    innobackupex --user=root --password=abc123456 --apply-back /data/backup/full/2022-04-15_05-09-07/
    #还原数据
    innobackupex --user=root --password=abc123456 --copy-back  /data/backup/full/2022-04-15_05-09-07/
    ```

    重新创建其余 4 个节点，组件 PXC 集群
