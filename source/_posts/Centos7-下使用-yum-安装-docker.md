---
title: Centos7 下使用 yum 安装 docker
tags:
  - docker
  - 笔记
date: 2019-05-11 11:11:19
---

> 本文是关于 docker-ce 的安装

### 使用 root 权限

```bash
su -
```

### 移除旧的版本

<!--more-->

```bash
$yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-selinux \
                  docker-engine-selinux \
                  docker-engine
```

### 安装一些必要的系统工具

```bash
$yum install -y yum-utils device-mapper-persistent-data lvm2
```

### 添加软件源信息

```bash
$yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```

### 更新 yum 缓存

```bash
$yum makecache fast
```

### 安装 Docker-ce

```bash
$yum -y install docker-ce
```

### 启动 Docker

```bash
$systemctl start docker
```

### 验证 Docker 是否安装成功

```bash
$docker run hello-world
```

### 设置 Docker 开机启动

```bash
$systemctl enable docker
```