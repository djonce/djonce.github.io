---
layout: post
title: "pm2 logrotate"
description: ""
category:
tags: [pm2]
---

{% include JB/setup %}

## pm2-logrotate

是一个 pm2 的插件, 日志管理。

## 安装使用

**1\. 安装**

```
pm2 install pm2-logrotate
```

**2\. 查看默认配置**

```
pm2 conf
```

可看到默认配置如下图所示，其配置文件一般在:`/root/.pm2/module_conf.json`：

```
$ pm2 set pm2-logrotate:max_size 10M
$ pm2 set pm2-logrotate:retain 30
$ pm2 set pm2-logrotate:compress false
$ pm2 set pm2-logrotate:dateFormat YYYY-MM-DD_HH-mm-ss
$ pm2 set pm2-logrotate:workerInterval 30
$ pm2 set pm2-logrotate:rotateInterval 0 0 * * *
$ pm2 set pm2-logrotate:rotateModule true
```

- `max_size` (Defaults to `10M`): 配置项默认是 10MB，并不意味着切割出来的日志文件大小一定就是 10MB，而是检查时发现日志文件大小达到 max_size，则触发日志切割。
- `retain` (Defaults to `30` file logs): 这个数字是在任何一个时间保留已分割的日志的数量，这意味着如果您保留 7 个，那么您将最多有 7 个已分割日志和您当前的一个
- `compress` (Defaults to `false`): 对所有已分割的日志启用 gzip 压缩
- `dateFormat` (Defaults to `YYYY-MM-DD_HH-mm-ss`) : 文件名格式化的规则
- `rotateModule` (Defaults to `true`) : 像其他应用程序一样分割 pm2 模块的日志
- `workerInterval` (Defaults to `30` in secs) : 您可以控制工作线程检查日志大小的间隔(最小值为`1`）单位为秒（控制模块检查 log 日志大小的循环时间，默认 30s 检查一次）
- `rotateInterval` (Defaults to `0 0 * * *` everyday at midnight): 多久备份一次，默认值是 0 0 \* \* \*，意思是每天晚上 0 点分割

**3\. 如何配置**

```
pm2 set pm2-logrotate:max_size 500K
pm2 set pm2-logrotate:retain 7
pm2 set pm2-logrotate:compress true
pm2 set pm2-logrotate:workerInterval 60
```

**4\. 使其生效**

```
pm2 restart all
```
