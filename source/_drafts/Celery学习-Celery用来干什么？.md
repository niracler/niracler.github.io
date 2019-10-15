---
title: Celery学习|Celery用来干什么？
tags:
    - celery
    - 笔记
---

我这里的学习记录是关于 Celery4.3.0 ，因为网上很多教程不是太旧了，就是没有说明 Celery 的版本是多少，于是我不得不去自己看官方的英文文档了。当然，还有一个很重要的原因，因为我正在做的项目是基于 Python3.7 的，而 Python3.7 竟然和 Celery4.3.0 以下的版本不兼容(因为[Support `async` keyword in python 3.7 ](https://github.com/celery/celery/issues/4500))。

## Rabbitmq

1. 关于这个的安装，直接用docker最省心了，
2. p 消息的生产者
3. 队列
4. c 消息的消费者

## 参考文章

- [Celery 4.3.0 documentation](http://docs.celeryproject.org/en/latest/getting-started/first-steps-with-celery.html)