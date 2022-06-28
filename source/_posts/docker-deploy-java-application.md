---
title: docker-deploy-java-application
date: 2022-06-28 09:37:56
tags:
- docker
- java
---
# 方法一

### 编写Dockerfile

```dockerfile
# 指定java版本号11
FROM openjdk:11
## 添加作者
MAINTAINER myf
# 指向的一个临时文件，用于存储tomcat工作。
VOLUME /tmp
# 复制jar包
ADD psc-0.0.1-SNAPSHOT.jar psc-0.0.1-SNAPSHOT.jar
# 声明要暴露的端口
EXPOSE 8081
# 容器启动时运行的命令
ENTRYPOINT ["java", "-jar"]
CMD ["psc-0.0.1-SNAPSHOT.jar"]
```

### 创建镜像

```shell
docker build -t psc .
```

### 查看构建的镜像

```shell
doucker images
```

### 部署项目

```shell
docker run -d -p 8081:8081 --name psc psc
```

- **-d** 以后台方式交互运行
- **-p** 暴露端口号，第一个是本机linux系统端口号，第二个是容器内的端口号
- **--name** 给容器起个名字，方便管理
- **psc** 镜像的名字

### 查看日志

```xshell
docker logs -f --tail=100 psc
```

- **-f** 跟踪日志输出
- **--tail** 仅列出最新N条日志
- **-t** 显示时间戳
- **--since :**显示某个开始时间的所有日志