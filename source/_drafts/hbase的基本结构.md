---
title: hbase的基本结构
tags:
  - hadoop
  - hbase
---

### region服务器

一个region服务器上有很多个region， 每一个region都是由很多的store构成的，每一个store里面都是由很多个HFile里面。

在每一store里面都有一个缓存。装满了再一次过commit上去

### region

### store

### HFILE

### MemStore

### 这个表结构为什么要这样设置？

### 那个列族是用来干什么的？

### 
