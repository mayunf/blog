---
title: Nginx开启gzip 来提高页面加载速度
date: 2018-12-11 11:58:17
tags:
- nginx
---
# Nginx开启gzip 来提高页面加载速度

## 配置文件`nginx.conf`

```bash
gzip on;
gzip_min_length 1k;
gzip_buffers 4 16k;
#gzip_http_version 1.0;
gzip_comp_level 2;
gzip_types text/plain application/javascript application/x-javascript text/css application/xml text/javascript application/x-httpd-php image/jpeg image/gif image/png;
gzip_vary off;
gzip_disable "MSIE [1-6]\.";

```

第1行：开启Gzip

第2行：不压缩临界值，大于1K的才压缩，一般不用改

第3行：buffer，就是，嗯，算了不解释了，不用改

第4行：用了反向代理的话，末端通信是HTTP/1.0，有需求的应该也不用看我这科普文了；有这句的话注释了就行了，默认是HTTP/1.1

第5行：压缩级别，1-10，数字越大压缩的越好，时间也越长，看心情随便改吧

第6行：进行压缩的文件类型，缺啥补啥就行了，JavaScript有两种写法，最好都写上吧，总有人抱怨js文件没有压缩，其实多写一种格式application/javascript 就行了

第7行：跟Squid等缓存服务有关，on的话会在Header里增加"Vary: Accept-Encoding"，我不需要这玩意，自己对照情况看着办吧

第8行：IE6对Gzip不怎么友好，不给它Gzip了


参考文档：

[https://blog.csdn.net/bigtree_3721/article/details/79849503](https://blog.csdn.net/bigtree_3721/article/details/79849503)
