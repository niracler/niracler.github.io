---
title: 使用 docker 快速部署 Redis
tags:
  - docker
  - redis
date: 2019-05-11 15:29:02
---

## 正常的使用 docker 命令部署的方法

因为分布式爬虫需要用到 redis ， 所以我就试着搭建一个 redis 用于学习学习

### 最简单的启动方法

```bash
$docker run --name my-redis -d redis
```

- ```-d``` 是让容器在后台启动  
- ```--name my-redis``` 为这容器命名
- ```redis``` 镜像名

<!--more-->

### 以持久化方式启动

```bash
$docker run --name my-redis -v ./data:/data -d redis redis-server --appendonly yes
```

- ```-v ./data:/data``` 将持久化的数据映射出容器外
- ```redis-server --appendonly yes``` 容器的执行命令，这里就是 redis 的持久化启动命令

### 映射 redis 的 6379 端口

要是不映射出来的话，只能在 docker 容器内访问

```bash
$docker run --name my-redis -p 6379:6379 -v $(pwd)/data:/data -d redis redis-server --appendonly yes
```

## 测试是否安装成功

> 注意，客户端也要安装 redis 才能使用 redis-cli 命令

```bash
$redis-cli -h 127.0.0.1 -p 6379
```

看来我是成功了？？

![oo](/images/Screenshot_20190511_152444.png)