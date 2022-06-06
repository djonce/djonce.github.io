---
layout: post
title: "jekyll docker 部署"
description: ""
category: jekyll
tags: []
---
{% include JB/setup %}


### 文档
https://github.com/envygeeks/jekyll-docker/blob/master/README.md

### 快速启动命令

``` shell

docker run --rm \
  --volume="$PWD:/srv/jekyll:Z" \
  --publish [::1]:4000:4000 \
  jekyll/jekyll \
  jekyll serve

```



