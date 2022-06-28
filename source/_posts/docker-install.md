---
title: docker 安装
date: 2022-06-28 09:31:38
tags: docker
---
## 在线安装

### 第一步：安装一组工具

```
sudo yum install -y yum-utils 
```

### 第二步：设置 yum 仓库地址

```
sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
sudo yum-config-manager \
     --add-repo \
     http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```

### 第三步：更新 yum 缓存

```
sudo yum makecache fast #yum 是包管理器
```

### 第四步：安装新版 docker

```
sudo yum install -y docker-ce docker-ce-cli containerd.io
```

### 第五步：开启Docker

```
systemctl start docker
```

### 第六步：检查安装状态

```
docker info
```

### 第七步：设置开机自启动

```
systemctl enable docker
```

### 第八步：Docker镜像加速

```shell
cat <<EOF > /etc/docker/daemon.json
{
  "registry-mirrors": [
    "https://docker.mirrors.ustc.edu.cn",
    "http://hub-mirror.c.163.com"
  ],
  "max-concurrent-downloads": 10,
  "log-driver": "json-file",
  "log-level": "warn",
  "log-opts": {
    "max-size": "10m",
    "max-file": "3"
    },
  "data-root": "/var/lib/docker"
}
EOF
```

### 第九步：重新启动Docker服务

```undefined
 systemctl restart docker
```

## 安装Docker-compose

```shell
sudo curl -L https://get.daocloud.io/docker/compose/releases/download/1.25.1/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
```

