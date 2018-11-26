---
title: Redis将key分组，删除匹配通配符的key
date: 2018-11-26 16:26:16
tags:
- redis
---

## 如何将key分组

> 使用冒号（:）分组或者其他的自定义前缀方式，如果我们需要清除redis特定的key内容时，在命令行下又没有直接的命令可用，可以使用linux的xargs参数或者第三方工具

## 命令行批量删除redis的key

> 首先linux服务器上需要安装redis客户端，然后进入到redis-cli命令所在的目录

```bash
redis-cli -h 127.0.0.1 -p 6379 -a 123456  keys 'test*' | xargs  redis-cli -h 127.0.0.1 -p 6379 -a 123456 del
```

- h redis 的ip地址
- p redis 的端口
- keys 通配符
- a redis 密码



参考文档：[https://www.jianshu.com/p/b3b6f3146325](https://www.jianshu.com/p/b3b6f3146325)
