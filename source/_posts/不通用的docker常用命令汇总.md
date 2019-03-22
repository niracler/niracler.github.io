---
title: 不通用的docker常用命令汇总
tags:
  - docker
date: 2019-03-22 11:43:31
---


最全面的介绍在[docker官方文档](https://docs.docker.com/)

### root权限

> 注意：docker命令的使用大多需要权限， 所以先使用root用户

```bash
$sudo su
```

<!-- more -->

### 删除所有容器与镜像

列出所有容器ID,查看所有运行或者不运行容器
```bash
$docker ps -aq
```

停止 ```docker ps -aq``` 所列出的容器, (才能够删除其中的images)

```bash
$docker stop $(docker ps -aq)
```

删除所有容器：

```bash
$docker rm $(docker ps -aq)
```
查看当前有些什么镜像

```bash
$docker images
```

删除全部镜像

```bash
$docker rmi $(docker images -q)
```

### 删除镜像
通过image的id来指定删除谁

```bash
$docker rmi <image id>
```

想要删除untagged images，也就是那些id为的image的话可以用

```bash
$docker rmi $(docker images | grep "^<none>" | awk "{print $3}")
```

强制删除全部image的话
```bash
$docker rmi -f $(docker images -q)
```

删除所有不使用的镜像
```bash
$docker image prune -f -a
```

### 删除容器

删除所有停止的容器

```bash
$docker container prune
```

### 容器与宿主机之间的文件传输

从容器到宿主机复制

```bash
$docker cp tomcat：/webapps/js/text.js /home/admin
$docker cp 容器名:  容器路径       宿主机路径         
```

从宿主机到容器复制

```bash
$docker cp /home/admin/text.js tomcat：/webapps/js
$docker cp 宿主路径中文件      容器名  容器路径   
```



### 停止、启动、杀死、重启一个容器

```bash
$docker stop Name或者ID  
$docker start Name或者ID  
$docker kill Name或者ID  
$docker restart name或者ID
```

### 进入容器

> 我认为进入容器是一种不优雅的做法，因为我认为进入容器要做的事情应该在Dockerfile里面

```bash
$docker exec -it  容器名或ID /bin/bash
```

- docker exec ：在运行的容器中执行命令
- -d :分离模式: 在后台运行
- -i :即使没有附加也保持STDIN（标准输入） 打开,以交互模式运行容器，通常与 -t 同时使用；
- -t: 为容器重新分配一个伪输入终端，通常与 -i 同时使用；

### 查看容器日志
查看容器日志
```bash
$docker logs <id/container_name>
```
实时查看日志输出
```bash
$docker logs -f <id/container_name> (类似 tail -f) (带上时间戳-t）
```

## 参考文章

[Docker删除容器与镜像](https://blog.csdn.net/qq_32447301/article/details/79387649)  
[Docker 命令大全](http://www.runoob.com/docker/docker-command-manual.html)
