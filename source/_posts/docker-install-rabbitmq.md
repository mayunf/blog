---
title: docker-install-rabbitmq
date: 2022-06-28 09:37:13
tags:
- docker
- rabbitmq
---
## 下载RabbitMQ镜像

``` shell
docker pull rabbitmq
```

## 创建并启动RabbitMQ容器

- 15672 端口用于网页访问
- 5672 端口用于生产和消息端使用

``` shell
docker run -id --hostname myrabbit --name rabbitmq1 -p 15672:15672 -p 5672:5672 rabbitmq
```

## 进入容器交互页面

``` shell
docker exec -it rabbitmq1 /bin/bash
```

## 下载插件

``` shell
rabbitmq-plugins enable rabbitmq_management
```

## 开放防火墙的端口

``` shell
[root@localhost mayunfeng]# firewall-cmd --add-port=15672/tcp --add-port=5672/tcp --permanent
success
[root@localhost mayunfeng]# firewall-cmd --reload 
success
[root@localhost mayunfeng]# firewall-cmd --list-ports 
22/tcp 80/tcp 443/tcp 15672/tcp 5672/tcp
```



## 进行访问

账号和密码均为：guest

