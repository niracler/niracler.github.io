---
title: centos7 关闭防火墙
tags:
  - centos
date: 2019-05-11 16:59:06
---


## centos7 关闭防火墙

关闭

```bash
$systemctl stop firewalld.service
```

禁止开机启动

```bash
$systemctl disable firewalld.service
```