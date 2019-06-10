---
title: 使用docker搭建hadoop伪分布式
tags:
  - docker
  - hadoop
  - 大数据
date: 2019-03-29 10:10:43
---

这篇文章应该算是我大数据系列的第一篇文章吧,路漫漫其修远兮，我本来不想这么快就往大数据方向前进的，只是这边项目需要，不得不学。本文章对新手极不友好。前置技能有很多，你总得安装个docker吧,还要了解Docker常用命令, 以及Dockerfile，docker-compose的使用。我的[不通用的Python程序员练级攻略](https://www.niracler.com/2019/03/14/%E4%B8%8D%E9%80%9A%E7%94%A8%E7%9A%84Python%E7%A8%8B%E5%BA%8F%E5%91%98%E7%BB%83%E7%BA%A7%E6%94%BB%E7%95%A5/)里有相应的学习资料。而该项目的地址是 https://github.com/Niracler/bigdata-exercise

这篇文章主要讲的是使用docker搭建hadoop伪分布式的细节，假如觉得麻烦的话，有更简便的方法。我这里先说为敬,或者你们可以先试试成功的喜悦。

将这个项目克隆下来，我的Dockerfile都是基于[这个项目](https://github.com/big-data-europe/docker-hadoop)改写的：

```bash
$git clone https://github.com/Niracler/bigdata-exercise.git
$cd bigdata-exercise/docker-hadoop/
```

启动

```bash
$sudo su
$docker-compose -f docker-compose-local.yml up -d
```

访问http://localhost:50070/ 效果

![](/images/Screenshot_20190329_103435.png)

进入容器

```bash
$docker exec -it namenode bash
```

上传文件测试

```bash
$touch test
$hdfs dfs -put test  /
$hdfs dfs -ls /
```

![](/images/Screenshot_20190410_104008.png)

然后？就没有然后了，下面是dockerfile等文件的细节

<!-- more -->
---

## 构建镜像

### 目录结构

你要先在该目录结构下

![](/images/Screenshot_20190329_080604.png)

### 构建hadoop-base镜像

hadoop-base的Dockerfile，之后具体的Dockerfile都是基于该Dockerfile,该Dockerfile的主要工作是下载jdk与hadoop

```bash
FROM openjdk:8
MAINTAINER Niracler <niracler@gmail.com>

# 下载并安装hadoop http://mirrors.tuna.tsinghua.edu.cn/apache/hadoop/common/hadoop-2.7.7/hadoop-2.7.7.tar.gz
ENV HADOOP_VERSION 2.7.7
ENV HADOOP_URL http://mirrors.tuna.tsinghua.edu.cn/apache/hadoop/common/hadoop-$HADOOP_VERSION/hadoop-$HADOOP_VERSION.tar.gz

# 解压，放到指定位置，并删除
RUN set -x \
    && curl -fSL "$HADOOP_URL" -o /tmp/hadoop.tar.gz \
    && tar -xvf /tmp/hadoop.tar.gz -C /opt/ \
    && rm /tmp/hadoop.tar.gz*

RUN ln -s /opt/hadoop-$HADOOP_VERSION/etc/hadoop /etc/hadoop
RUN cp /etc/hadoop/mapred-site.xml.template /etc/hadoop/mapred-site.xml
RUN mkdir /opt/hadoop-$HADOOP_VERSION/logs

RUN mkdir /hadoop-data

# 设置环境变量
ENV HADOOP_PREFIX=/opt/hadoop-$HADOOP_VERSION
ENV HADOOP_CONF_DIR=/etc/hadoop
ENV MULTIHOMED_NETWORK=1

ENV USER=root
ENV PATH $HADOOP_PREFIX/bin/:$PATH

# 添加启动文件
ADD entrypoint.sh /entrypoint.sh
RUN chmod a+x /entrypoint.sh

ENTRYPOINT ["/entrypoint.sh"]
```

启动文件entrypoint.sh

```bash
#!/bin/bash

# Set some sensible defaults
export CORE_CONF_fs_defaultFS=${CORE_CONF_fs_defaultFS:-hdfs://`hostname -f`:8020}

function addProperty() {
  local path=$1
  local name=$2
  local value=$3

  local entry="<property><name>$name</name><value>${value}</value></property>"
  local escapedEntry=$(echo $entry | sed 's/\//\\\//g')
  sed -i "/<\/configuration>/ s/.*/${escapedEntry}\n&/" $path
}

function configure() {
    local path=$1
    local module=$2
    local envPrefix=$3

    local var
    local value

    echo "Configuring $module"
    for c in `printenv | perl -sne 'print "$1 " if m/^${envPrefix}_(.+?)=.*/' -- -envPrefix=$envPrefix`; do
        name=`echo ${c} | perl -pe 's/___/-/g; s/__/@/g; s/_/./g; s/@/_/g;'`
        var="${envPrefix}_${c}"
        value=${!var}
        echo " - Setting $name=$value"
        addProperty /etc/hadoop/$module-site.xml $name "$value"
    done
}

configure /etc/hadoop/core-site.xml core CORE_CONF
configure /etc/hadoop/hdfs-site.xml hdfs HDFS_CONF
configure /etc/hadoop/yarn-site.xml yarn YARN_CONF
configure /etc/hadoop/httpfs-site.xml httpfs HTTPFS_CONF
configure /etc/hadoop/kms-site.xml kms KMS_CONF

if [ "$MULTIHOMED_NETWORK" = "1" ]; then
    echo "Configuring for multihomed network"

    # HDFS
    addProperty /etc/hadoop/hdfs-site.xml dfs.namenode.rpc-bind-host 0.0.0.0
    addProperty /etc/hadoop/hdfs-site.xml dfs.namenode.servicerpc-bind-host 0.0.0.0
    addProperty /etc/hadoop/hdfs-site.xml dfs.namenode.http-bind-host 0.0.0.0
    addProperty /etc/hadoop/hdfs-site.xml dfs.namenode.https-bind-host 0.0.0.0
    addProperty /etc/hadoop/hdfs-site.xml dfs.client.use.datanode.hostname true
    addProperty /etc/hadoop/hdfs-site.xml dfs.datanode.use.datanode.hostname true

    # YARN
    addProperty /etc/hadoop/yarn-site.xml yarn.resourcemanager.bind-host 0.0.0.0
    addProperty /etc/hadoop/yarn-site.xml yarn.nodemanager.bind-host 0.0.0.0
    addProperty /etc/hadoop/yarn-site.xml yarn.nodemanager.bind-host 0.0.0.0
    addProperty /etc/hadoop/yarn-site.xml yarn.timeline-service.bind-host 0.0.0.0

    # MAPRED
    addProperty /etc/hadoop/mapred-site.xml yarn.nodemanager.bind-host 0.0.0.0
fi

if [ -n "$GANGLIA_HOST" ]; then
    mv /etc/hadoop/hadoop-metrics.properties /etc/hadoop/hadoop-metrics.properties.orig
    mv /etc/hadoop/hadoop-metrics2.properties /etc/hadoop/hadoop-metrics2.properties.orig

    for module in mapred jvm rpc ugi; do
        echo "$module.class=org.apache.hadoop.metrics.ganglia.GangliaContext31"
        echo "$module.period=10"
        echo "$module.servers=$GANGLIA_HOST:8649"
    done > /etc/hadoop/hadoop-metrics.properties

    for module in namenode datanode resourcemanager nodemanager mrappmaster jobhistoryserver; do
        echo "$module.sink.ganglia.class=org.apache.hadoop.metrics2.sink.ganglia.GangliaSink31"
        echo "$module.sink.ganglia.period=10"
        echo "$module.sink.ganglia.supportsparse=true"
        echo "$module.sink.ganglia.slope=jvm.metrics.gcCount=zero,jvm.metrics.memHeapUsedM=both"
        echo "$module.sink.ganglia.dmax=jvm.metrics.threadsBlocked=70,jvm.metrics.memHeapUsedM=40"
        echo "$module.sink.ganglia.servers=$GANGLIA_HOST:8649"
    done > /etc/hadoop/hadoop-metrics2.properties
fi

exec $@
```

构建hadoop-base镜像

```bash
$docker build -t="hadoop-base" ./base
```

### 构建hadoop-namenode

hadoop-namenode的Dockerfile

```bash
FROM hadoop-base
MAINTAINER niracler <niracler@gmail.com>

HEALTHCHECK CMD curl -f http://localhost:50070/ || exit 1

ENV HDFS_CONF_dfs_namenode_name_dir=file:///hadoop/dfs/name
RUN mkdir -p /hadoop/dfs/name
VOLUME /hadoop/dfs/name

ADD run.sh /run.sh
RUN chmod a+x /run.sh

EXPOSE 50070

CMD ["/run.sh"]
```

启动文件run.sh

```bash
#!/bin/bash

namedir=`echo $HDFS_CONF_dfs_namenode_name_dir | perl -pe 's#file://##'`
if [ ! -d $namedir ]; then
  echo "Namenode name directory not found: $namedir"
  exit 2
fi

if [ -z "$CLUSTER_NAME" ]; then
  echo "Cluster name not specified"
  exit 2
fi

if [ "`ls -A $namedir`" == "" ]; then
  echo "Formatting namenode name directory: $namedir"
  $HADOOP_PREFIX/bin/hdfs --config $HADOOP_CONF_DIR namenode -format $CLUSTER_NAME
fi

$HADOOP_PREFIX/bin/hdfs --config $HADOOP_CONF_DIR namenode
```

构建hadoop-namenode镜像

```bash
$docker build -t="hadoop-namenode" ./namenode
```

### 构建hadoop-datanode

hadoop-datanode的Dockerfile

```bash
FROM hadoop-base
MAINTAINER niracler <niracler@gmail.com>

HEALTHCHECK CMD curl -f http://localhost:50075/ || exit 1

ENV HDFS_CONF_dfs_datanode_data_dir=file:///hadoop/dfs/data
RUN mkdir -p /hadoop/dfs/data
VOLUME /hadoop/dfs/data

ADD run.sh /run.sh
RUN chmod a+x /run.sh

EXPOSE 50075

CMD ["/run.sh"]
```

启动文件run.sh

```bash
#!/bin/bash

datadir=`echo $HDFS_CONF_dfs_datanode_data_dir | perl -pe 's#file://##'`
if [ ! -d $datadir ]; then
  echo "Datanode data directory not found: $datadir"
  exit 2
fi

$HADOOP_PREFIX/bin/hdfs --config $HADOOP_CONF_DIR datanode
```

构建hadoop-datanode镜像

```bash
$docker build -t="hadoop-datanode" ./datanode
```

### 构建hadoop-nodemanager

hadoop-nodemanager的Dockerfile

```bash
FROM hadoop-base
MAINTAINER Niracler <niracler@gmail.com>

HEALTHCHECK CMD curl -f http://localhost:8042/ || exit 1

ADD run.sh /run.sh
RUN chmod a+x /run.sh

EXPOSE 8042

CMD ["/run.sh"]
```

启动文件run.sh

```bash
#!/bin/bash

$HADOOP_PREFIX/bin/yarn --config $HADOOP_CONF_DIR nodemanager
```

构建hadoop-nodemanager

```bash
$docker build -t="hadoop-nodemanager" ./nodemanager
```

### 构建hadoop-resourcemanager

hadoop-resourcemanager的Dockerfile

```bash
FROM hadoop-base
MAINTAINER niracler <niracler@gmail.com>

HEALTHCHECK CMD curl -f http://localhost:8088/ || exit 1

ADD run.sh /run.sh
RUN chmod a+x /run.sh

EXPOSE 8088

CMD ["/run.sh"]
```

启动文件run.sh

```bash
#!/bin/bash

$HADOOP_PREFIX/bin/yarn --config $HADOOP_CONF_DIR resourcemanager
```

```bash
$docker build -t="hadoop-resourcemanager" ./resourcemanager
```

### 构建hadoop-historyserver

hadoop-historyserver的Dockerfile

```bash
FROM hadoop-base
MAINTAINER niracler <niracler@gmail.com>

HEALTHCHECK CMD curl -f http://localhost:8188/ || exit 1

ENV YARN_CONF_yarn_timeline___service_leveldb___timeline___store_path=/hadoop/yarn/timeline
RUN mkdir -p /hadoop/yarn/timeline
VOLUME /hadoop/yarn/timeline

ADD run.sh /run.sh
RUN chmod a+x /run.sh

EXPOSE 8188

CMD ["/run.sh"]
```

启动文件run.sh

```bash
#!/bin/bash

$HADOOP_PREFIX/bin/yarn --config $HADOOP_CONF_DIR historyserver
```

```bash
$docker build -t="hadoop-historyserver" ./historyserver
```

### 配置文件

```
CORE_CONF_fs_defaultFS=hdfs://namenode:8020
CORE_CONF_hadoop_http_staticuser_user=root
CORE_CONF_hadoop_proxyuser_hue_hosts=*
CORE_CONF_hadoop_proxyuser_hue_groups=*

HDFS_CONF_dfs_webhdfs_enabled=true
HDFS_CONF_dfs_permissions_enabled=false

YARN_CONF_yarn_log___aggregation___enable=true
YARN_CONF_yarn_resourcemanager_recovery_enabled=true
YARN_CONF_yarn_resourcemanager_store_class=org.apache.hadoop.yarn.server.resourcemanager.recovery.FileSystemRMStateStore
YARN_CONF_yarn_resourcemanager_fs_state___store_uri=/rmstate
YARN_CONF_yarn_nodemanager_remote___app___log___dir=/app-logs
YARN_CONF_yarn_log_server_url=http://historyserver:8188/applicationhistory/logs/
YARN_CONF_yarn_timeline___service_enabled=true
YARN_CONF_yarn_timeline___service_generic___application___history_enabled=true
YARN_CONF_yarn_resourcemanager_system___metrics___publisher_enabled=true
YARN_CONF_yarn_resourcemanager_hostname=resourcemanager
YARN_CONF_yarn_timeline___service_hostname=historyserver
YARN_CONF_yarn_resourcemanager_address=resourcemanager:8032
YARN_CONF_yarn_resourcemanager_scheduler_address=resourcemanager:8030
YARN_CONF_yarn_resourcemanager_resource___tracker_address=resourcemanager:8031
```

### 启动

这里使用docker-compose,docker-compose-loc.yml内容如下:

```yml
version: "2"

services:
  namenode:
    image: hadoop-namenode
    hostname: namenode
    container_name: namenode
    volumes:
      - ./data/namenode:/hadoop/dfs/name
    environment:
      - CLUSTER_NAME=test
    env_file:
      - ./hadoop.env
    ports:
      - "50070:50070"

  resourcemanager:
    image: hadoop-resourcemanager
    hostname: resourcemanager
    container_name: resourcemanager
    depends_on:
      - "namenode"
    links:
      - "namenode"
    env_file:
      - ./hadoop.env

  historyserver:
    image: hadoop-historyserver
    hostname: historyserver
    container_name: historyserver
    volumes:
      - ./data/historyserver:/hadoop/yarn/timeline
    depends_on:
      - "namenode"
    links:
      - "namenode"
    env_file:
      - ./hadoop.env

  nodemanager1:
    image: hadoop-nodemanager
    hostname: nodemanager1
    container_name: nodemanager1
    depends_on:
      - "namenode"
      - "resourcemanager"
    links:
      - "namenode"
      - "resourcemanager"
    env_file:
      - ./hadoop.env

  datanode1:
    image: hadoop-datanode
    hostname: datanode1
    container_name: datanode1
    depends_on:
      - "namenode"
    links:
      - "namenode"
    volumes:
      - ./data/datanode1:/hadoop/dfs/data
    env_file:
      - ./hadoop.env

  datanode2:
    image: hadoop-datanode
    hostname: datanode2
    container_name: datanode2
    depends_on:
      - "namenode"
    links:
      - "namenode"
    volumes:
      - ./data/datanode2:/hadoop/dfs/data
    env_file:
      - ./hadoop.env

  datanode3:
    image: hadoop-datanode
    hostname: datanode3
    container_name: datanode3
    depends_on:
      - "namenode"
    links:
      - "namenode"
    volumes:
      - ./data/datanode3:/hadoop/dfs/data
    env_file:
      - ./hadoop.env
```

启动

```bash
$docker-compose -f docker-compose-local.yml up -d
```

### 访问 http://localhost:50070/ 效果

![](/images/Screenshot_20190329_100435.png)


## 参考文章

-   [使用docker搭建hadoop分布式集群](https://blog.csdn.net/xu470438000/article/details/50512442)  
-   [CentOS更换阿里云YUM源](https://www.jianshu.com/p/38c9e98ec481)   
-   [docker-hadoop](https://github.com/big-data-europe/docker-hadoop)  
-   [debian系linux，更换apt-get官方源为国内源](https://blog.csdn.net/yjk13703623757/article/details/78943345)  
-   [如何使用Apt-Get在Debian 8上安装Java](https://www.howtoing.com/how-to-install-java-with-apt-get-on-debian-8)  
-   [debian8无痛安装JDK8](https://www.jianshu.com/p/1cf26c0c9e9b)
-   [hadoop安装](https://www.apache.org/dyn/closer.cgi/hadoop/common/hadoop-2.7.7/hadoop-2.7.7.tar.gz)
-   [HBase Python API](https://www.cnblogs.com/fanghao/p/8542170.html)
-   [基于 docker 搭建 hadoop 跨主机集群](https://hacpai.com/article/1508232710946)
-   [Docker常用命令](https://segmentfault.com/a/1190000012063374)
-   [使用Docker Swarm模式搭建Swarm集群](https://www.jianshu.com/p/df744c4e375e)
-   [Hadoop: …be replicated to 0 nodes instead of minReplication (=1). There are 1 datanode(s) running and no node(s) are excluded in this operation](https://stackoverflow.com/questions/36015864/hadoop-be-replicated-to-0-nodes-instead-of-minreplication-1-there-are-1/36310025)
