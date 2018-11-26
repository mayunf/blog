---
title: learn-docker
date: 2018-11-20 20:54:37
tags:
---

记录docker学习内容

## docker 常用命令

1. 看所有镜像的列表
```bash
sudo docker images
```
2. 使用阿里云docker镜像加速

```bash
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://85uexsvl.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```

参考资料[https://yq.aliyun.com/articles/29941](https://yq.aliyun.com/articles/29941)

3. docker 常用子命令

```bash
images    显示镜像列表

history   显示镜像构建历史

commit    从容器创建新镜像

build     从 Dockerfile 构建镜像

tag       给镜像打 tag

pull      从 registry 下载镜像

push      将 镜像 上传到 registry

rmi       删除 Docker host 中的镜像

search    搜索 Docker Hub 中的镜像
```
/usr/local/php/bin/php /export/webapps/weixin.jwsem.com/artisan 
