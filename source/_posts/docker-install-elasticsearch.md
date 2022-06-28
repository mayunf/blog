---
title: docker-install-elasticsearch
date: 2022-06-28 09:35:09
tags:
- git
- elasticsearch
---
# 单节点安装



## 下载Elasticsearch镜像

``` shell
docker pull elasticsearch:7.6.2
```

## 创建网桥

``` she
docker network create es-net
```

## 创建docker容器挂在的目录

``` shell
mkdir -p /opt/elasticsearch-7.6.2/config /opt/elasticsearch-7.6.2/data /opt/elasticsearch-7.6.2/plugins
```

## 配置文件

``` shell
echo "http.host: 0.0.0.0" >> /opt/elasticsearch-7.6.2/config/elasticsearch.yml
```

## 创建容器

```shell
sudo docker run --name elasticsearch -p 9200:9200  -p 9300:9300 \
 -e "discovery.type=single-node" \
 -e ES_JAVA_OPTS="-Xms128m -Xmx512m" \
 -v /opt/es_docker/config/elasticsearch.yml:/opt/elasticsearch-7.6.2/config/elasticsearch.yml \
 -v /opt/es_docker/data:/opt/elasticsearch-7.6.2/elasticsearch/data \
 -v /opt/es_docker/plugins:/opt/elasticsearch-7.6.2/plugins \
 -d elasticsearch:7.6.2
```

### 说明

- 9200 用于外部通讯，基于http协议，程序与es的通信使用9200端口。
- 9300 jar之间就是通过tcp协议通信，遵循tcp协议，es集群中的节点之间也通过9300端口进行通信。
- -p 端口映射
- -e discovery.type=single-node 单点模式启动
- -e ES_JAVA_OPTS="-Xms128m-Xmx512m"：设置启动占用的内存范围
- -v 目录挂载
- -d 后台运行

## 开放防火墙的端口

``` shell
[root@localhost mayunfeng]# firewall-cmd --add-port=9200/tcp --permanent
success
[root@localhost mayunfeng]# firewall-cmd --reload 
success
[root@localhost mayunfeng]# firewall-cmd --list-ports 
22/tcp 80/tcp 443/tcp 9200/tcp
```



## 进行访问

ip:9200

账号和密码均为：guest

# 集群安装

## 调整虚拟内存

```shell
#修改文件
sudo vim /etc/sysctl.conf
 
#添加参数
...
vm.max_map_count = 262144
# 重新加载/etc/sysctl.conf配置
sysctl -p
```

## 编写docker-compose.yaml

```yaml
version: "2.2"
services:
  es01:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.6.2
    container_name: es01
    ports:
      - 9200:9200
      - 9300:9300
    environment:
      - node.name=es01
      - cluster.name=es-docker-cluster
      - discovery.seed_hosts=es02,es03
      - cluster.initial_master_nodes=es01,es02,es03
      - bootstrap.memory_lock=true
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    ulimits:
      memlock:
        soft: -1
        hard: -1
    volumes:
      - data01:/usr/share/elasticsearch/data
    networks:
      - elastic
  es02:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.6.2
    container_name: es02
    environment:
      - node.name=es02
      - cluster.name=es-docker-cluster
      - discovery.seed_hosts=es01,es03
      - cluster.initial_master_nodes=es01,es02,es03
      - bootstrap.memory_lock=true
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    ulimits:
      memlock:
        soft: -1
        hard: -1
    volumes:
      - data02:/usr/share/elasticsearch/data
    networks:
      - elastic
  es03:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.6.2
    container_name: es03
    environment:
      - node.name=es03
      - cluster.name=es-docker-cluster
      - discovery.seed_hosts=es01,es02
      - cluster.initial_master_nodes=es01,es02,es03
      - bootstrap.memory_lock=true
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    ulimits:
      memlock:
        soft: -1
        hard: -1
    volumes:
      - data03:/usr/share/elasticsearch/data
    networks:
      - elastic
  cerebro:
    image: lmenezes/cerebro
    container_name: cerebro
    ports:
      - 9000:9000
    networks:
      - elastic

volumes:
  data01:
    driver: local
  data02:
    driver: local
  data03:
    driver: local

networks:
  elastic:
    driver: bridge
```

- cerebro 为集群可视化工具
- es01 es02 es03 是集群的节点

## 启动集群

> 以集群的方式启动ElasticSearch

```shell
docker-compose up -d
```

