---
title: 不通用的 Python 程序员练级攻略
date: 2019-03-14 16:48:59
tags:
    - python
---

> 本文章记录的是笔者在立志成为 python 程序员的目标制定及技术点亮过程，主要有我看过的觉得可以的资源，以及我自己的总结，
> 还夹带着一些无聊的日常


![技术栈。jpg](https://upload-images.jianshu.io/upload_images/4781155-2c23e3e36cbf3c27.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

保持更新的技术栈[思维导图](https://www.processon.com/view/link/5c04f065e4b0059238d440d1)

<!-- more -->

## 目录

## python

- [廖雪峰的 python](https://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000)
    可能是最好的 python 中文教程

### 代码风格

### 高级特性

web
---

### 前端

- [npm 换源](https://www.jianshu.com/p/0deb70e6f395)

### 后端

- [Django 基础教程 by
    自强学堂](https://code.ziqiangxuetang.com/django/django-tutorial.html)
    第一次接触可以看这个
- [Python 前后端分离开发 Vue+Django REST
    framework 实战](https://www.bilibili.com/video/av40066981?from=search&seid=5778159097686358187)

## 爬虫

- [Python 3 网络爬虫开发实战
    , 崔庆才著』(https://pan.baidu.com/s/1dxLM6hDhLQ4yrR0iRcID2Q)
    关于爬虫的资料跟书籍首推 崔庆才的爬虫书，总的来说，我感觉有点像官方文档
- [崔庆才 python3 爬虫入门到精通](https://www.bilibili.com/video/av37497888)
    b 站视频

### 前端分析

### 框架

> 目前使用过的爬虫框架只有 scrapy

-   [How To Use Scrapy
    Item](https://blog.michaelyin.info/scrapy-tutorial-9-how-use-scrapy-item/)
    使用 SQLAlchemy 优雅地解决 scrapy 连接 mysql 数据库的问题

### 请求

### 动态页面爬取

### APP 爬取

### 数据库

### 代理池

### 小灵感

数据结构与算法
--------------

### 数据结构

### 算法

## 数据挖掘&机器学习

### 有监督

### 无监督

### 数据预处理

### 统计分析

- [pandas 速查表](https://www.dataquest.io/blog/large_files/pandas-cheat-sheet.pdf)
    当然要学过的才会看、
- [《利用 Python 进行数据分析·第 2 版》](https://www.jianshu.com/p/04d180d90a3f)
    pandas 库的作者写的，你说好不好？
- [Sklearn 与 TensorFlow
    机器学习实用指南』(https://github.com/apachecn/hands-on-ml-zh)

### 数据可视化

- [python networkx
    包绘制复杂网络关系图](https://www.jianshu.com/p/e543dc63454f)

## 运维

### 自动化部署

### 监控

### 负载

## 中间件

### server

[virsh 的使用与实例步骤安装虚拟机](https://blog.csdn.net/panfengyun12345/article/details/16878873)

```bash

systemctl enable libvirtd
systemctl start libvirtd

yum install qemu-kvm libvirt virt-install ebtables bridge-utils

scp -r root@172.28.7.39:/root/create_br0.sh create_br0.sh

virt-install --virt-type kvm --name CentOS-7-x86_64-C00 --ram 4096 \
    --disk path=/home/raw/CentOS-7-x86_64-C00.raw,device=disk,bus=virtio \
    --network bridge:br0 --graphics vnc,listen=0.0.0.0,port=5900 --noautoconsole \
    --network bridge=br0,model=virtio  --boot hd

virt-install --virt-type kvm --name CentOS-7-x86_64-C00 --vcpus=4 --ram 4096 \
    --os-type linux --os-variant=rhl7 \
    --disk path=/home/raw/CentOS-7-x86_64-C00.raw,device=disk,bus=virtio \
    --network bridge:br0,model=virtio --graphics vnc,listen=0.0.0.0,port=6900 \
    --noautoconsole --boot hd

virt-clone -n CentOS-7-x86_64-C01 -o CentOS-7-x86_64-C00 -f /home/raw/CentOS-7-x86_64-C01.raw
```

### 文件服务器

### 容器

- [Docker 從入門到實踐](https://legacy.gitbook.com/book/philipzheng/docker_practice/details)
- [Docker 原理圖解及全環境安裝](https://legacy.gitbook.com/book/joshhu/docker_theory_install/details)
- [自己学 Docker:10.Docker.v1.12 升级到 v1.13.0 正式版](https://blog.csdn.net/mungo/article/details/54632686)
    关于 docker 的版本升级可以看这里
- [优雅地解决 docker 容器中 mysql 的编码问题](https://stackoverflow.com/questions/46004648/how-to-setup-mysql-with-utf-8-using-docker-compose)
- [使用 docker 搭建 hadoop 分布式集群](https://blog.csdn.net/xu470438000/article/details/50512442)  
- [CentOS 更换阿里云 YUM 源](https://www.jianshu.com/p/38c9e98ec481)   
- [docker-hadoop](https://github.com/big-data-europe/docker-hadoop)  
- [debian 系 linux，更换 apt-get 官方源为国内源](https://blog.csdn.net/yjk13703623757/article/details/78943345)  
- [如何使用 Apt-Get 在 Debian 8 上安装 Java](https://www.howtoing.com/how-to-install-java-with-apt-get-on-debian-8)  
- [debian8 无痛安装 JDK8](https://www.jianshu.com/p/1cf26c0c9e9b)
- [hadoop 安装](https://www.apache.org/dyn/closer.cgi/hadoop/common/hadoop-2.7.7/hadoop-2.7.7.tar.gz)
- [HBase Python API](https://www.cnblogs.com/fanghao/p/8542170.html)
- [基于 docker 搭建 hadoop 跨主机集群](https://hacpai.com/article/1508232710946)
- [Docker 常用命令](https://segmentfault.com/a/1190000012063374)
- [使用 Docker Swarm 模式搭建 Swarm 集群](https://www.jianshu.com/p/df744c4e375e)
- [非 root 用户加入 docker 用户组省去 sudo](https://blog.csdn.net/u013948858/article/details/78429954) 之后要重启
- [CentOS Docker 安装](https://www.runoob.com/docker/centos-docker-install.html)

## 开发环境

### 编辑器 &IDEs

- [pycharm](https://www.jetbrains.com/pycharm/) python 最强大的 IDE
- [atom](https://atom.io/) 我写 markdown 文档就是用这个
- [30 个极大提高开发效率的 Visual Studio Code 插件](https://blog.fundebug.com/2018/07/24/vs-extensions/)
- [Visual Studio Code 有哪些你常用的插件？](https://www.zhihu.com/question/40640654)

### 版本控制

- [Git 教程 - 廖雪峰的官方网站](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)\
- [GitHub 团队项目合作流程](https://www.cnblogs.com/schaepher/p/4933873.html)
- [Git 克隆时不带。git 文件夹](https://stackoverflow.com/questions/11497457/git-clone-without-git-directory)

### 画图

-   [processon](https://www.processon.com/)
    我是挺喜欢这个在线画图网站的，就是有时候有 bug
-   [Upgrade-ProcessOn](https://github.com/96chh/Upgrade-ProcessOn) 增加
    ProcessOn 的免费文件数（逃)

操作系统
--------

> 因为笔者开发时使用的是 linux 操作系统的 manjaro 发行版，其中踩过无数的坑，
> 也做过一些笔记

### linux 那些坑

-   [Manjaro linux 安装笔记](https://www.jianshu.com/p/6e9eb98c0494)
    无数次重装系统后我都是对着着文档安装软件的，当年帮我装这系统的人写的
-   [本人的安装 manjaro 常用软件的文档](manjaro/Manjaro_install.txt)
-   [Manjaro Linux 配置 Intel 与 Nvidia 双显卡切换](https://mtaoist.xyz/2018/03/19/Bumblebee/)
    因为显卡问题而无法进入系统时，也可以用这里面的方法解决
-   [给本子安装 manjaro 出现无法关机的解决办法](https://blog.csdn.net/Tangcuyuha/article/details/80298500)
    当时也是因为显卡问题
-   [No wired network (Realtek RTL8111/8168/8411)](https://forum.manjaro.org/t/no-wired-network-realtek-rtl8111-8168-8411/22501)
    以太网卡找不到的问题

### 操作系统笔记

-   [操作系统常见概念](computer_system/)

数据
----

### 关系型数据库

-   [mysql]()
-   [mongodb]()

### 标记语言

-   [Markdown------ 入门指南](https://www.jianshu.com/p/1e402922ee32)
-   [实用帖 \| 如何为 Markdown 文件自动生成目录？](https://www.jianshu.com/p/4721ddd27027)
-   [Pandoc](https://pandoc.org/MANUAL.html) 感觉我会喜欢上这个工具的
-   [pandoc word to markdown with images](https://stackoverflow.com/questions/39956497/pandoc-convert-docx-to-markdown-with-embedded-images)

程序人生
--------

### 日记

> 我认为， 我们要时刻知道自己在干什么， 并定时地审视自己 -

- [niracler 的博客](https://niracler.github.io/)

### hexo

-   [使用 hexo+github 免费搭建个人博客网站超详细教程](https://www.jianshu.com/p/a39573555039)

-   [使用 Hexo 创建项目文档网站](https://github.com/nodejh/nodejh.github.io/issues/37)

-   [blog+github 备份和恢复](https://jinzequn.github.io/2018/01/24/github-hexo/)

-   [Hexo 博客 Next 主题打开过慢的解决办法](http://www.mdslq.cn/archives/f186e832.html)

### 面试

- [关于 python 的面试题](https://github.com/kenwoodjw/python_interview_question)

## 提高效率的软件推荐

### v2ray

- [v2ray](http://einverne.github.io/post/2018/01/v2ray.html#linux)

## 其他

- [西方经济学复习笔记](economics-notes/)

## 本项目参考内容

- [整个 IT 行业的技术栈是怎样的？](https://www.zhihu.com/question/28799035)
- [python 程序员之路](https://github.com/ethanww/dive-into-python)
- [【Java 学习 + 面试指南】](https://github.com/Snailclimb/JavaGuide)
    一份涵盖大部分 Java 程序员所需要掌握的核心知识。\
- [fullstackpython](https://www.fullstackpython.com/table-of-contents.html)
    我看了这个，差点都不想继续写了、
