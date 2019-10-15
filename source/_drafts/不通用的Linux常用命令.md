---
title: 不通用的Linux常用命令
tags:
  - linux
---

# linux下查看最消耗CPU、内存的进程

1. CPU占用最多的前10个进程：

```bash
ps auxw|head -1;ps auxw|sort -rn -k3|head -10
```

1. 内存消耗最多的前10个进程  

```bash
ps auxw|head -1;ps auxw|sort -rn -k4|head -10
```

1. 虚拟内存使用最多的前10个进程  

```bash
ps auxw|head -1;ps auxw|sort -rn -k5|head -10
```